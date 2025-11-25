
# Central de Mídia - Homelab

Projeto para orquestração de uma central de mídia completa em Kubernetes, utilizando ArgoCD para GitOps e aplicações open source para download, organização e consumo de mídia.

## Estrutura do Projeto

```
media/
├── bazarr/
├── homarr/
├── jellyfin/
├── jellyseerr/
├── prowlarr/
├── qbittorrent/
├── radarr/
├── sonarr/
└── setup/
```

Cada aplicação possui:
- Manifests Kubernetes para Deployment, Service, Ingress e Persistent Volumes.
- Arquivos de configuração para integração com ArgoCD.

O diretório `setup/` contém configurações do projeto e repositório para o ArgoCD.

## Aplicações

- **Jellyfin**: Servidor de mídia para streaming de vídeos, músicas e fotos.
- **Sonarr**: Gerenciador de séries de TV, com integração para download automático.
- **Radarr**: Gerenciador de filmes, com integração para download automático.
- **Bazarr**: Gerenciador de legendas para Sonarr e Radarr.
- **qBittorrent**: Cliente torrent para downloads automatizados.
- **Jellyseerr**: Sistema de requisições de mídia para usuários do Jellyfin.
- **Homarr**: Dashboard para centralizar o acesso às aplicações.
- **Prowlarr**: Indexador de torrents e usenet.

## Volumes e Armazenamento

- Utiliza volumes persistentes locais (`local-path`) para configuração, downloads e biblioteca de mídia.
- Estrutura de armazenamento sugerida:
	- `/volumes/ssd/k3s/` para configurações e cache
	- `/volumes/hdd/media/` para downloads, séries e filmes

Exemplo de PersistentVolume:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
	name: media-pv
spec:
	capacity:
		storage: 1.5Ti
	accessModes:
		- ReadOnlyMany
	persistentVolumeReclaimPolicy: Delete
	storageClassName: local-path
	hostPath:
		path: /volumes/hdd/media
```

## Gerenciamento via ArgoCD

- O projeto é gerenciado pelo ArgoCD, facilitando o versionamento e o deploy contínuo das aplicações.
- Configurações do projeto e repositório estão em `setup/project.yaml` e `setup/repository.yaml`.

## Passos para Instalação

1. **Altere o arquivo `setup/repository.yaml`:**
	- Insira sua chave SSH privada no campo `sshPrivateKey`.
	- Altere o campo `url` para o seu repositório Git (ex: `git@github.com:seu-usuario/seu-repo.git`).
	- Se necessário, ajuste o campo `urlBase` para refletir o domínio base do seu ambiente.

2. **Altere o arquivo `setup/project.yaml`:**
	- Atualize o campo `sourceRepos` para o seu repositório Git.
	- Ajuste o campo `urlBase` se aplicável.

3. **Aplique os manifests do setup:**
	```sh
	kubectl apply -f setup/
	```

4. **Altere, dentro de cada pasta de aplicação, o arquivo `setup/application.yaml`**
	- Atualize o campo `urlBase` para o domínio base do seu ambiente.

5. **Altere, dentro de cada pasta/values, os arquivos YAML**
	- Ajuste os caminhos de volumes, domínios de ingress e demais configurações para refletir o ambiente do seu host.

6. **Aplique os manifestos da aplicação**
	```sh
	#ex: kubectl apply -f bazarr/setup
    kubectl apply -f aplicacao/setup
	```

7. **Acompanhe o deploy pelo ArgoCD:**
	- Acesse a interface do ArgoCD e sincronize as aplicações.

8. **Acesse as aplicações:**
	- Use os domínios configurados nos ingressos para acessar cada serviço.

## Observações

- Para upgrades das aplicações, siga as recomendações oficiais de cada projeto.

---
Projeto mantido por [davidcardoso-homelab](https://github.com/davidcardoso-homelab/).

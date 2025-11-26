# Bitmagnet

Bitmagnet é um servidor DHT crawler que indexa metadados de torrents de forma descentralizada, permitindo buscas rápidas e sem dependência de trackers centralizados.

## Visão Geral

Este deployment do Bitmagnet em Kubernetes executa:
- **HTTP Server**: Interface web na porta 3333
- **BitTorrent TCP/UDP**: Crawler DHT operando nas portas 3334 (TCP e UDP)
- **Queue Server**: Processamento de fila para indexação
- **DHT Crawler**: Rastreamento descentralizado de metadados de torrents

## Características

- 🔍 **Indexação Descentralizada**: Coleta metadados de torrents via DHT
- 🎬 **Integração TMDB**: Enriquecimento de dados de filmes e séries
- 📊 **Banco de Dados PostgreSQL**: Armazenamento persistente de índices
- 🎨 **Interface Web**: Dashboard para busca e gestão
- 🔧 **Configuração Flexível**: Filtros customizáveis via classifier.yml

## Configuração

### Variáveis de Ambiente

| Variável | Descrição | Valor Padrão |
|----------|-----------|--------------|
| `POSTGRES_HOST` | Host do banco de dados PostgreSQL | - |
| `POSTGRES_NAME` | Nome do banco de dados | - |
| `POSTGRES_USER` | Usuário do PostgreSQL | - |
| `POSTGRES_PASSWORD` | Senha do PostgreSQL | - |
| `TMDB_API_KEY` | Chave da API do TMDB | - |
| `CLASSIFIER_DELETE_XXX` | Filtrar conteúdo adulto | `true` |
| `DHT_CRAWLER_SAVE_FILES_THRESHOLD` | Limite de arquivos para salvar | `500` |

### API Key TMDB

Para obter uma API KEY privada do TMDB:

1. Acesse [The Movie Database (TMDB)](https://www.themoviedb.org/)
2. Crie uma conta (se não tiver)
3. Vá em Configurações → API
4. Solicite uma API Key (v3 auth)
5. Aceite os termos e preencha o formulário
6. Copie sua chave de API

Referência: https://bitmagnet.io/setup/configuration.html#obtaining-a-tmdb-api-key

### Classifier Configuration

O arquivo `classifier.yml` define filtros para categorização e exclusão de conteúdo:

```yaml
flags:
  delete_xxx: true                    # Remove conteúdo adulto
  delete_content_types:
    - xxx                             # Conteúdo adulto
    - audiobook                        # Audiolivros
    - music                            # Música
    - comic                            # Quadrinhos
```

## Recursos

### CPU
- **Requests**: 250m
- **Limits**: 600m

### Memória
- **Requests**: 512Mi
- **Limits**: 700Mi

## Portas

| Porta | Protocolo | Serviço |
|-------|-----------|---------|
| 3333 | TCP | Web UI / HTTP Server |
| 3334 | TCP | BitTorrent Protocol |
| 3334 | UDP | BitTorrent DHT |

## Volumes

- **bitmagnet-config-pvc**: Armazenamento de configurações e dados do indexador

## Saúde e Monitoramento

- **Liveness Probe**: Verifica `/status` a cada 30s
- **Readiness Probe**: Verifica `/status` a cada 30s
- **Timeout**: 5 segundos
- **Falhas toleradas**: 3

## Integração com o Stack

O Bitmagnet trabalha em conjunto com:
- **Prowlarr**: Para indexação de torrents complementar
- **Sonarr/Radarr**: Para busca de metadados enriquecidos
- **qBittorrent**: Como cliente de download

## Próximas Etapas

1. Configurar credenciais PostgreSQL no secret `bitmagnet-postgres-secret`
2. Adicionar TMDB_API_KEY ao mesmo secret
3. Aplicar o manifesto via ArgoCD: `kubectl apply -f setup/application.yaml`
4. Acessar a interface web em http://bitmagnet.local:3333 (ou conforme seu ingress)

## Referências

- [Documentação Bitmagnet](https://bitmagnet.io/)
- [Configuração Bitmagnet](https://bitmagnet.io/setup/configuration.html)
- [TMDB API Documentation](https://developer.themoviedb.org/docs)
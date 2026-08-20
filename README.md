# Servidor TeamSpeak 3 com Docker

## Requisitos

- Docker com o plugin Docker Compose
- As portas `9987/UDP` e `30033/TCP` liberadas no firewall do servidor

## Subir o servidor

```powershell
Copy-Item .env.example .env
docker compose up -d
docker compose logs -f teamspeak
```

Na primeira inicializacao, procure nos logs o **ServerAdmin privilege key**. Copie a chave e guarde-a em local seguro; ela concede administracao total do servidor.

No TeamSpeak Client, conecte usando:

```text
IP_PUBLICO_DO_SERVIDOR:9987
```

Quando solicitado, use a privilege key obtida nos logs.

## Portas

| Porta | Protocolo | Uso |
| --- | --- | --- |
| 9987 | UDP | Voz e conexao dos clientes |
| 30033 | TCP | Transferencia de arquivos |
| 10011 | TCP | ServerQuery, acessivel somente no proprio host |

Se o servidor estiver em casa, encaminhe `9987/UDP` e `30033/TCP` no roteador para o computador que executa o Docker. Nao exponha `10011/TCP` publicamente sem configurar controles de acesso.

## Comandos uteis

```powershell
# Acompanhar os logs
docker compose logs -f teamspeak

# Parar sem apagar os dados
docker compose down

# Iniciar novamente
docker compose up -d

# Atualizar a imagem
docker compose pull
docker compose up -d
```

As configuracoes, canais e permissoes ficam no volume Docker `teamspeak-data` e sobrevivem a recriacoes do container.

## Backup

Pare o container antes de fazer uma copia consistente do volume:

```powershell
docker compose stop teamspeak
docker run --rm -v ts3_teamspeak-data:/data -v ${PWD}:/backup alpine tar czf /backup/teamspeak-backup.tar.gz -C /data .
docker compose start teamspeak
```

O nome real do volume pode variar conforme o nome da pasta/projeto. Confira antes com `docker volume ls`.


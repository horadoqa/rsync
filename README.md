# O que é o rsync

O rsync sincroniza arquivos comparando origem e destino, copiando apenas o que mudou.

Principais vantagens:

* rápido
* economiza banda
* mantém permissões
* funciona localmente e via SSH
* ideal para backup

Site oficial:

[rsync official site](https://rsync.samba.org/?utm_source=chatgpt.com)

---

# Estrutura básica do comando

```bash
rsync [opções] origem destino
```

Exemplo simples:

```bash
rsync -av pasta1/ pasta2/
```

---

# Significado das opções mais usadas

| Opção        | Função                                            |
| ------------ | ------------------------------------------------- |
| `-a`         | modo arquivo (preserva permissões, datas etc.)    |
| `-v`         | mostra detalhes                                   |
| `-h`         | tamanhos legíveis                                 |
| `--progress` | mostra progresso                                  |
| `--delete`   | remove no destino o que não existe mais na origem |
| `-z`         | compacta durante transferência                    |
| `-n`         | simulação (não executa)                           |

---

# Exemplo prático 1 — Backup local

## Cenário

Você quer copiar:

```bash
/home/joao/documentos
```

para:

```bash
/backup/documentos
```

---

## Passo 1 — Criar pasta de backup

```bash
mkdir -p /backup/documentos
```

---

## Passo 2 — Executar rsync

```bash
rsync -avh --progress /home/joao/documentos/ /backup/documentos/
```

---

## Resultado

O rsync vai:

* copiar arquivos novos
* atualizar arquivos modificados
* preservar permissões
* mostrar progresso

---

# IMPORTANTE: barra `/` no final

Existe diferença:

## Com barra

```bash
rsync -av pasta/ destino/
```

Copia o **conteúdo** da pasta.

---

## Sem barra

```bash
rsync -av pasta destino/
```

Copia a pasta inteira.

---

# Exemplo prático 2 — Backup remoto via SSH

## Cenário

Enviar arquivos para servidor:

* IP: `192.168.0.50`
* usuário: `ubuntu`

---

## Comando

```bash
rsync -avz --progress /home/joao/site/ ubuntu@192.168.0.50:/var/www/site/
```

---

## O que acontece

* conecta via SSH
* compacta os dados (`-z`)
* envia apenas alterações
* mostra progresso

---

# Exemplo prático 3 — Espelhamento completo

## Objetivo

Deixar destino exatamente igual à origem.

---

## Comando

```bash
rsync -av --delete origem/ destino/
```

---

## Atenção ao `--delete`

Ele remove arquivos do destino que não existem mais na origem.

Muito útil para:

* espelhamento
* backups sincronizados

Mas perigoso se usado errado.

---

# Simular antes de executar

Use `-n`:

```bash
rsync -avhn --delete origem/ destino/
```

Isso mostra o que seria feito sem alterar nada.

---

# Exemplo real de rotina de backup

## Script

```bash
#!/bin/bash

ORIGEM="/home/joao/"
DESTINO="/backup/joao/"

rsync -avh --delete --progress $ORIGEM $DESTINO
```

---

## Tornar executável

```bash
chmod +x backup.sh
```

---

## Executar

```bash
./backup.sh
```

---

# Automatizar com cron

Editar cron:

```bash
crontab -e
```

Executar todo dia às 2h:

```bash
0 2 * * * /home/joao/backup.sh
```

---

# Excluir arquivos/pastas

## Ignorar `.git`

```bash
rsync -av --exclude='.git' projeto/ backup/
```

---

## Ignorar múltiplos

```bash
rsync -av \
  --exclude='node_modules' \
  --exclude='*.log' \
  projeto/ backup/
```

---

# Verificando diferenças

```bash
rsync -avnc origem/ destino/
```

* `-n` = não executa
* `-c` = compara checksum

---

# Com SSH em porta diferente

```bash
rsync -avz -e "ssh -p 2222" arquivos/ user@servidor:/backup/
```

---

# Restaurando backup

Basta inverter origem e destino:

```bash
rsync -av /backup/documentos/ /home/joao/documentos/
```

---

# Fluxo recomendado para uso seguro

1. testar com `-n`
2. revisar arquivos
3. executar sem `-n`
4. automatizar
5. usar logs

---

# Exemplo profissional completo

```bash
rsync -avh \
  --delete \
  --progress \
  --exclude='node_modules' \
  --exclude='*.log' \
  /home/joao/projeto/ \
  ubuntu@192.168.0.50:/backup/projeto/
```

---

# Erros comuns

## 1. Esquecer a barra `/`

Pode mudar completamente o resultado.

---

## 2. Usar `--delete` sem testar

Pode apagar arquivos importantes.

---

## 3. Permissão negada

Resolver com:

```bash
sudo rsync ...
```

ou configurar SSH corretamente.

---

# Dica avançada

Para backups versionados, muita gente combina:

* rsync
* hard links
* snapshots
* cron

Isso economiza muito espaço.

---

# Resumo rápido

## Backup local

```bash
rsync -avh origem/ destino/
```

## Backup remoto

```bash
rsync -avz origem/ user@ip:/destino/
```

## Espelhamento

```bash
rsync -av --delete origem/ destino/
```

## Simulação

```bash
rsync -avn origem/ destino/
```

Outros modos de utilização:

* rsync para Docker
* rsync em Windows
* rsync + SSH key
* backup incremental profissional
* sincronização entre servidores Linux
* rsync com NAS/Synology
* comparação rsync vs scp vs rclone
* arquitetura ideal de backup com rsync

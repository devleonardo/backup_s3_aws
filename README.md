# Upload Firewall Backups to S3

Este repositório contém um playbook do Ansible que automatiza o upload de backups de firewalls para um bucket S3 na AWS. O script localiza arquivos de backup criados nas últimas 24 horas e os transfere para um bucket S3 com a classe de armazenamento **INTELLIGENT_TIERING**, ideal para arquivos com acessos imprevisíveis, otimizando o custo de armazenamento.

## Requisitos

- **Ansible** instalado (versão 2.9+ recomendada)
- **Python 3** e o **AWS CLI** configurado com permissões para acessar o bucket S3
- Acesso à AWS com as permissões necessárias para upload no S3
- **Credenciais da AWS** armazenadas no arquivo `~/.aws/credentials`

## Configuração

### 1. AWS CLI

Certifique-se de que o AWS CLI esteja instalado e possua as permissões descritas acima.


## 2. Variáveis
No playbook, ajuste o caminho do diretório de backups e o bucket S3 de destino, se necessário:

- Diretório de backup: /etc/backup_firewalls
- Bucket S3: backup-fortigate

### Estrutura do Playbook

O playbook possui as seguintes tarefas:

1. **Encontrar arquivos de backup**: Pesquisa por arquivos no diretório especificado criados nas últimas 24 horas.
2. **Upload para o S3**: Realiza o upload dos arquivos encontrados para o bucket S3 com a classe de armazenamento INTELLIGENT_TIERING.
3. **Log do status do upload**: Exibe uma mensagem com o total de arquivos carregados.

## Uso
Para executar o playbook, use o comando:

```py
ansible-playbook upload_firewall_backups.yml
```
## Exemplo de Saída
Ao executar o playbook, você deve ver uma saída semelhante à seguinte:

```py
TASK [Find new backup files created in the last 24 hours] **********************
ok: [localhost]

TASK [Upload new files to S3 with INTELLIGENT_TIERING storage class] ***********
changed: [localhost] => (item=/etc/backup_firewalls/backup1.tar.gz)
changed: [localhost] => (item=/etc/backup_firewalls/backup2.tar.gz)

TASK [Log upload status] *******************************************************
ok: [localhost] => {
    "msg": "Total files uploaded: 2"
}
```

### Também é possível adicionar uma configuração de execução automática:
Para executar automaticamente o playbook todos os dias às 5:00 AM, siga os passos abaixo para configurar um cron job.

1. Crie um script chamado master.sh no diretório /etc/scripts (ou outro de sua preferência) para chamar o playbook:

````py
#!/bin/bash
ansible-playbook /caminho/para/seu/playbook/backup.yaml
````
2. Torne o script executável:

````py
chmod +x /etc/scripts/master.sh
````

3. Adicione a seguinte entrada ao cron para agendar a execução diária do script às 5:00 AM:
````py
crontab -e
````

````py
0 5 * * * /usr/bin/sh /etc/scripts/master.sh
````

4. Verifique o cron job com:

````py
crontab -l
````

Com essas configurações, o cron executará o playbook backup.yaml automaticamente todos os dias às 5:00 AM.

## Observações
- Certifique-se de que o usuário configurado no AWS CLI tenha permissões adequadas para acessar e fazer upload no bucket S3.
- Para personalizar o período de tempo dos arquivos de backup, ajuste a opção -ctime -1 no comando find do playbook.

## Contribuição
Sinta-se à vontade para abrir issues ou enviar pull requests para melhorias no playbook.

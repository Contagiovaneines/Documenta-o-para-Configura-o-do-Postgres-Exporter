# Documenta-o-para-Configura-o-do-Postgres-Exporter
Documentação para Configuração do Postgres Exporter

Esta documentação orienta na instalação e configuração do Postgres Exporter para monitorar um banco de dados PostgreSQL com Prometheus.

## Passo 1: Criar Diretório e Baixar o Postgres Exporter

1. Criar um diretório para o Postgres Exporter:
   ```bash
   mkdir /opt/postgres_exporter
   ```

2. Navegar até o diretório criado:
   ```bash
   cd /opt/postgres_exporter
   ```

3. Baixar o arquivo do Postgres Exporter:
   ```bash
   wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.15.0/postgres_exporter-0.15.0.linux-amd64.tar.gz
   ```

4. Baixar manualmente (opcional):
   Alternativamente, você pode baixar o arquivo manualmente abrindo o link [aqui](https://github.com/prometheus-community/postgres_exporter/releases) no seu navegador e baixando a versão desejada. Depois, mova o arquivo baixado para o diretório `/opt/postgres_exporter`.

5. Descompactar o arquivo baixado:
   ```bash
   tar xvfz postgres_exporter-0.15.0.linux-amd64.tar.gz
   ```

## Passo 2: Mover Executável

1. Navegar até o diretório descompactado:
   ```bash
   cd postgres_exporter-0.15.0.linux-amd64
   ```

2. Copiar o executável para `/usr/local/bin`:
   ```bash
   sudo cp postgres_exporter /usr/local/bin
   ```

3. Retornar ao diretório original:
   ```bash
   cd /opt/postgres_exporter
   ```

## Passo 3: Configurar Variáveis de Ambiente

1. Criar o arquivo de variáveis de ambiente:
   ```bash
   sudo nano postgres_exporter.env
   ```

2. Adicionar as variáveis de ambiente:

   Para um banco de dados específico:
   ```bash
   DATA_SOURCE_NAME="postgresql://usuario:senha@localhost:5432/nome_do_banco?sslmode=disable"
   ```

   Ou para monitorar todos os bancos de dados no localhost:
   ```bash
   DATA_SOURCE_NAME="postgresql://postgres:postgres@localhost:5432/?sslmode=disable"
   ```

3. Salvar e sair do editor:
   Pressione `Ctrl+O` para salvar o arquivo, `Enter` para confirmar e `Ctrl+X` para sair do editor nano.

## Passo 4: Criar Usuário para o Postgres Exporter (se necessário)

```bash
sudo useradd -rs /bin/false postgres
```

## Passo 5: Configurar o Serviço do Postgres Exporter

1. Criar o arquivo de serviço:
   ```bash
   sudo nano /etc/systemd/system/postgres_exporter.service
   ```

2. Adicionar a configuração do serviço:
   ```ini
   [Unit]
   Description=Prometheus exporter for Postgresql
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=postgres
   Group=postgres
   WorkingDirectory=/opt/postgres_exporter
   EnvironmentFile=/opt/postgres_exporter/postgres_exporter.env
   ExecStart=/usr/local/bin/postgres_exporter --web.listen-address=(ip_da_maquina):9187 --web.telemetry-path=/metrics
   Restart=always

   [Install]
   WantedBy=multi-user.target
   ```

3. Salvar e sair do editor:
   Pressione `Ctrl+O` para salvar o arquivo, `Enter` para confirmar e `Ctrl+X` para sair do editor nano.

## Passo 6: Habilitar e Iniciar o Serviço

1. Recarregar os daemons do systemd:
   ```bash
   sudo systemctl daemon-reload
   ```

2. Iniciar o serviço do Postgres Exporter:
   ```bash
   sudo systemctl start postgres_exporter
   ```

3. Habilitar o serviço para iniciar automaticamente no boot:
   ```bash
   sudo systemctl enable postgres_exporter
   ```

4. Verificar o status do serviço:
   ```bash
   sudo systemctl status postgres_exporter
   ```

## Passo 7: Verificar o Endpoint de Métricas

```bash
curl http://(ip_da_maquina):9187/metrics
```

Substitua `(ip_da_maquina)` pelo endereço IP da sua máquina. Se tudo estiver configurado corretamente, você verá as métricas expostas pelo Postgres Exporter.

## Revertendo a Configuração do Postgres Exporter

Para reverter os passos que você executou, você precisará remover os arquivos criados e desfazer as alterações de configuração.

1. Remover o diretório criado e os arquivos baixados:
   ```bash
   sudo rm -rf /opt/postgres_exporter
   ```

2. Remover o binário copiado:
   ```bash
   sudo rm /usr/local/bin/postgres_exporter
   ```

3. Remover o arquivo de configuração do serviço:
   ```bash
   sudo rm /etc/systemd/system/postgres_exporter.service
   ```

4. Recarregar os daemons do systemd para aplicar as alterações:
   ```bash
   sudo systemctl daemon-reload
   ```

5. Parar e desabilitar o serviço, caso esteja em execução:
   ```bash
   sudo systemctl stop postgres_exporter
   sudo systemctl disable postgres_exporter
   ```

6. Verificar o status do serviço para garantir que ele foi parado e desabilitado:
   ```bash
   sudo systemctl status postgres_exporter
   ```

Esses passos vão reverter todas as ações que você realizou, removendo os arquivos e diretórios criados, desfazendo as configurações de serviço e excluindo os usuários adicionados para a configuração do Postgres Exporter.

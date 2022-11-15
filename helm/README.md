# Oque é Helm ?

- Gerenciador de pacotes do mundo do Kubernetes

- Charts
    - São os pacotes no mundo do Helm
    - Possuem os arquivos de template e configuração necessários para criar os recursos do Kubernetes

- Histórico de Revisões
    - Guarda histórico das atualizações e instalações feitas

- Configuração Dinâmica
    - Preenchimento dinâmico dos arquivos do Kubernetes a partir de templates

- Dry run
    - Comando que ajuda a ver como serão criados os arquivos do Kubernetes antes de ser executado
    - Ajuda com debug
    - `helm install mydb bitnami/mysql --values values.yaml --dry-run`
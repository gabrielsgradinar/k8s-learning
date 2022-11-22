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

- Criação de Chart
    - `helm create chart_name` # por default cria um chart com nginx
    - Estrutura de um Chart
    ```
    Chart.yaml -> metadados do Chart
    charts/ -> onde outros Charts de dependência são copiados
    templates/ -> onde ficam os templates para a criação dos objetos do kubernetes
    values.yaml -> todos os valores padrão que serão usados nos templates
    ```
    - Instalação do Chart
        - `helm install firstapp /chart_folder`

- Templates
    - Actions
        - {{ }}
        - Dentro das Actions podemos usar lógicas de programação e funções
    - `.Values` -> contém todas as informações do `values.yaml`
    ```yaml
    {{.Values.my.custom.data}}
    {{.Chart.Name}}
    {{.Release.Name}}
    {{.Values.my.custom.data | default "testdefault" | upper | quote}}
    ```

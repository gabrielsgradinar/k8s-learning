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

    {{ $myFLAG := .Values.my.flag }}
    {{- if $myFLAG }}
    {{"Output of IF" | nindent 2}}
    {{- else}}
    {{"Output of ELSE" | nindent 2}}
    {{- end}}

    {{- with .Values.my.values}}
    {{- toYaml . | nindent 2 }}
    {{- end}}

    {{- range .Values.my.values}}
    - {{. | upper }}
    {{- end}}

    {{- range $key, $value := .Values.image}}
    - {{$key}}: {{$value}}
    {{- end}}
    ```

- Dependências
    - Em `Chart.yaml` podemos adicionar o setor de dependências do nosso chart
    ```yaml
    dependencies:
    - name: mysql
        version: "9.4.4"
        repository: "https://charts.bitnami.com/bitnami"
    ```
    - Para instalar as dependências `helm dependency update chart_folder`

- Hooks
    - A partir de algumas notações no `metadata` dos templates é possível executar algumas ações usando qualquer tipo de recurso do kubernetes
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: "{{ include "firstchart.fullname" . }}-pre-install"
    annotations:
        "helm.sh/hook": pre-install
        "helm.sh/hook-weight": "1"
        "helm.sh/hook-delete-policy": hook-succeeded
    spec:
    containers:
        - name: pre-install
        image: busybox
        imagePullPolicy: IfNotPresent
        command: ['sh', '-c', 'echo Pod is Running']
    restartPolicy: OnFailure
    ```

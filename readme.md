

# Kubernetes

---

## Sumário 📓

- [Namespaces](#namespaces)
- [Pods](#Pods)
- [Pods-2](#pods-2)
- [Deployments](#Deployments)
- [Deployments - 2](#Deployments - 2)
- [Services](#Services)
- [Volumes](#Volumes)
- [Cronjobs](#Cronjobs)
- [Secrets](#Secrets)
- [Configmaps](#Configmaps)
- [InitContainer](#InitContainer)
- [RBAC](#RBAC)
- [Helm](#Helm)
- [Ingress](#Ingress)
- [Assuntos-Extra](#Assuntos-Extra)
- [HPA](#HPA)
- [Statefulset](#Statefulset)
- [Extra](#Extra)

---

- **Instalando Clusters**
    
    
    Introdução
    
    - O Kubernetes é uma plataformas de orquestração de contêineres. A origem dele está em um algum lugar nos data centers do Google, onde nasceu o Borg, a plataforma interna de orquestração de contêineres do Google. O Google usou o Borg durante muitos anos para executar suas aplicações. Em 2014, a empresa decidiu transferir sua experiência com o Borg para um novo projeto de código aberto chamado de “Kubernetes” (palavra grega que quer dizer “Tinomoneiro” ou “Piloto”) e, em 2015, passou a ser o primeiro projeto doado para a recém-criada Cloud Native Computing Foundation (CNCF).
    - Comunidade bem ativa, principalmente no github
    - Plataforma como Serviço (Platform-as-a-Service)
    - O Red Hat OpenShift oferece bastante recursos adicionais ao Kubernetes
    
    - Segue uma doc criada por mim para instalar o Kind e kubectl
        
        [https://github.com/Erick-Fernandes-dev/Instalando_o_Kind_e_o_kubectl](https://github.com/Erick-Fernandes-dev/Instalando_o_Kind_e_o_kubectl)
        
    
    **Comando gera instruções de conclusão automática específicas para o Zsh**
    
    ```bash
    kind completion zsh
    ```
    
    Comando para que o autocomplete do kind funcione permanentemente.
    
    ```bash
    echo "source <(kind completion zsh)" >> ../.zshrc
    ```
    
    ---
    
    ### ***Instalando o Minikube 💻***
    
    ### [Requisitos básicos](https://github.com/badtuxx/DescomplicandoKubernetes/blob/main/pt/day_one/README.md#requisitos-b%C3%A1sicos)
    
    É importante frisar que o Minikube deve ser instalado localmente, e não em um *cloud provider*. Por isso, as especificações de *hardware* a seguir são referentes à máquina local.
    
    - Processamento: 1 core;
    - Memória: 2 GB;
    - HD: 20 GB.
    
    ### **Instalação do minikube no linux**
    
    Antes de mais nada, verifique se a sua máquina suporta virtualização. No 
    GNU/Linux, isto pode ser realizado com o seguinte comando:
    
    ```
    grep -E --color 'vmx|svm' /proc/cpuinfo
    
    ```
    
    Caso a saída do comando não seja vazia, o resultado é positivo.
    
    Há a possibilidade de não utilizar um *hypervisor* para a instalação do Minikube, executando-o ao invés disso sobre o próprio host. Iremos utilizar o Oracle VirtualBox como *hypervisor*, que pode ser encontrado [**aqui**](https://www.virtualbox.org/).
    
    Efetue o download e a instalação do `Minikube`utilizando os seguintes comandos.
    
    ```
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    
    chmod +x ./minikube
    
    sudo mv ./minikube /usr/local/bin/minikube
    
    minikube version
    ```
    
    Você pode então listar os nós que fazem parte do seu *cluster* k8s com o seguinte comando:
    
    ```
    kubectl get nodes
    
    ```
    
    A saída será similar ao conteúdo a seguir:
    
    ```
    NAME       STATUS   ROLES           AGE   VERSION
    minikube   Ready    control-plane   20s   v1.25.3
    
    ```
    
    **Para criar um `cluster com mais de um nó`, você pode utilizar o comando abaixo, apenas modificando os valores para o desejado:**
    
    ```
    minikube start --nodes 2 -p multinode-cluster
    
    😄  minikube v1.26.0 on Debian bookworm/sid
    ✨  Automatically selected the docker driver. Other choices: kvm2, virtualbox, ssh, none, qemu2 (experimental)
    📌  Using Docker driver with root privileges
    👍  Starting control plane node minikube in cluster minikube
    🚜  Pulling base image ...
    💾  Downloading Kubernetes v1.24.1 preload ...
        > preloaded-images-k8s-v18-v1...: 405.83 MiB / 405.83 MiB  100.00% 66.78 Mi
        > gcr.io/k8s-minikube/kicbase: 385.99 MiB / 386.00 MiB  100.00% 23.63 MiB p
        > gcr.io/k8s-minikube/kicbase: 0 B [_________________________] ?% ? p/s 11s
    🔥  Creating docker container (CPUs=2, Memory=8000MB) ...
    🐳  Preparing Kubernetes v1.24.1 on Docker 20.10.17 ...
        ▪ Generating certificates and keys ...
        ▪ Booting up control plane ...
        ▪ Configuring RBAC rules ...
    🔗  Configuring CNI (Container Networking Interface) ...
    🔎  Verifying Kubernetes components...
        ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    🌟  Enabled addons: storage-provisioner, default-storageclass
    
    👍  Starting worker node minikube-m02 in cluster minikube
    🚜  Pulling base image ...
    🔥  Creating docker container (CPUs=2, Memory=8000MB) ...
    🌐  Found network options:
        ▪ NO_PROXY=192.168.11.11
    🐳  Preparing Kubernetes v1.24.1 on Docker 20.10.17 ...
        ▪ env NO_PROXY=192.168.11.11
    🔎  Verifying Kubernetes components...
    🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
    
    ```
    
    Para visualizar os nós do seu novo cluster Kubernetes, digite:
    
    ```
    kubectl get nodes
    
    ```
    
    Inicialmente, a intenção do Minikube é executar o k8s em apenas um nó, 
    porém a partir da versão 1.10.1 é possível usar a função de multi-node.
    
    Caso os comandos anteriores tenham sido executados sem erro, a instalação do Minikube terá sido realizada com sucesso.
    
    ### **Ver detalhes sobre o cluster**
    
    ```
    minikube status
    ```
    
    ---
    
    ### **Descobrindo o endereço do Minikube**
    
    Como dito anteriormente, o Minikube irá criar uma máquina virtual, assim 
    como o ambiente para a execução do k8s localmente. Ele também irá 
    configurar o `kubectl` para comunicar-se com o Minikube. Para saber qual é o endereço IP dessa máquina virtual, pode-se executar:
    
    ```
    minikube ip
    
    ```
    
    O endereço apresentado é que deve ser utilizado para comunicação com o k8s.
    
    ---
    
    ### **Acessando a máquina do Minikube via SSH**
    
    Para acessar a máquina virtual criada pelo Minikube, pode-se executar:
    
    ```
    minikube ssh
    
    ```
    
    ---
    
    ### **Dashboard do Minikube**
    
    O Minikube vem com um *dashboard* *web* interessante para que o usuário iniciante observe como funcionam os *workloads* sobre o k8s. Para habilitá-lo, o usuário pode digitar:
    
    ```
    minikube dashboard
    
    ```
    
    ---
    
    ### **Logs do Minikube**
    
    Os *logs* do Minikube podem ser acessados através do seguinte comando.
    
    ```
    minikube logs
    
    ```
    
    ### **Remover o cluster**
    
    ```
    minikube delete
    
    ```
    
    Caso queira remover o cluster e todos os arquivos referente a ele, utilize o parametro *--purge*, conforme abaixo:
    
    `minikube delete --purge`
    
    ### Instalando e configurando cluster com máquinas virtutais com Vagrant
    
    Configuração do Vagrantfile
    
    ```bash
    Vagrant.configure("2") do |config|
        (1..1).each do |i|
            config.vm.define "master-#{i}" do |k8s|
                k8s.vm.box = "ubuntu/focal64"
                k8s.vm.hostname = "master-#{i}"
                #k8s.vm.network "private_network", ip: "172.89.0.1#{i}"
                k8s.vm.network "private_network", type: "static", ip: "192.168.56.11#{i}"
    
                k8s.ssh.insert_key = false
                k8s.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa']
                k8s.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
    
                k8s.vm.provider "virtualbox" do |vb|
                  vb.gui = false
                  vb.cpus = 2
                  vb.memory = "2048"
                end
            end
        end
    
        (1..2).each do |i|
            config.vm.define "worker-#{i}" do |k8s|
                k8s.vm.box = "ubuntu/focal64"
                k8s.vm.hostname = "worker-#{i}"
                #k8s.vm.network "private_network", ip: "172.89.0.2#{i}"
                k8s.vm.network "private_network", type: "static", ip: "192.168.56.12#{i}"
    
                k8s.ssh.insert_key = false
                k8s.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa']
                k8s.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
    
                k8s.vm.provider "virtualbox" do |vb|
                  vb.gui = false
                  vb.cpus = 1
                  vb.memory = "2048"
                end
            end
        end
    end
    ```
    
    Agora suba as máquinas virtuais para o virtualbox
    
    ```bash
    vagrant up
    ```
    
    Abra 3 terminais na pasta onde você subiu o vagrant e execute o seguinte comando:
    
    ```bash
    vagrant ssh master-1
    vagrant ssh worker-1
    vagrant ssh worker-2
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled.png)
    
    Agora, instale o docker nas 3 máquinas
    
    ```bash
    curl -fsSL https://get.docker.com | bash
    ```
    
    Em seguida, nas 3 máquinas crie um arquivo daemon.json no diretório /etc/docker/ e implemente a seguinte configuração.
    
    ```bash
    {
        "exec-opts": ["native.cgroupdriver=systemd"],
        "log-driver": "json-file",
        "log-opts": {
    	    "max-size": "100m"
        },
        "storage-driver": "overlay2"
    }
    
    # Salve e saia desse arquivo e execute os seguinte comandos
    #---------------------------------------------------------------
    mkdir -p /etc/systemd/system/docker.service.d
    systemctl daemon-reload
    systemctl restart docker
    docker container ls
    docker info | grep -i cgroup
    ```
    
    Agora instale a chave do pacote google cloud nas 3 máquinas
    
    ```bash
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    # OK
    ```
    
    Depois instale o kubernetes xenial nas 3 máquinas
    
    ```bash
    echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
    cat /etc/apt/sources.list.d/kubernetes.list
    apt-get update
    
    ```
    
    Agora instale nas 3 máquinas: kubeadm, kubelet e kubectl
    
    ```bash
    apt-get install -y kubeadm kubelet kubectl
    ```
    
    Em seguida, execute o seguinte comando na máquina master
    
    ```bash
    #Baixar as imagens
    kubeadm config images pull
    ```
    
- **Namespace**
    
    ## Namespaces
    
    - E um lugar isolado, cercadinho da aplicacao
    
    ```
    	# Lista os namespace
    	kubectl get namespace
    
    	# Listar Pods de um determinado mamespace
    	kubecttl get po --namespace kube-flanel
    	kubecttl get po -n kube-flanel
    
    	# Criando um namespace
    	kubectl create namespace <nomeNS>
    	kubectl create ns <nome>
    
    	# Simular a criacao de um namespace com o --dry-run e depois pega o yaml dele
    	kubectl create ns comunidade-devops --dry-run -oyaml
    
    	# Criando um namespace e jogando para dentro de um aqruivo yaml
    	kubectl create ns comunidade-devops --dry-run -oyaml | kubectl neat > ns.yaml
    
    ```
    
    **Yaml de como criar um namespace**
    
    ```yaml
    apiVersion: v1
    kind: Namespace
    metadata:
      name: comunidade-devops
    
    ```
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    	name: nginx
    	namespace: comunidade-devops
    spec:
    	containers:
    	- name: mginx
    	  image: nginx:1.14.2
    	  ports:
    	  - containerPort: 80
    
    ```
    
    - O —**dry-run** -> Ele finge que criou mas nao criou / simula a criacao de algo
    
    ```
    	#comando para mudar de namespace
    	kubectl config set-context --current --namespace comunidade-devops
    
    ```
    
- **Pod**
    
    
    ## Instalando o kubectl krew e o neat
    
    - kubectl krew é um tipo de instalador de plugins para o kubenertes
        - Como instalar o krew 👾🐧
        
        [https://krew.sigs.k8s.io/docs/user-guide/setup/install/](https://krew.sigs.k8s.io/docs/user-guide/setup/install/)
        
    - O neat é um encurtador de yamal. ele retira informações desnecessárias de um arquivo de configuração de objeto kubernetes yaml
        - **Rode o seguinte comando para instalar o neat**
        
        ```bash
        kubectl krew install neat
        ```
        
    
    ## Pods
    
    - Um pod e como se fosse uma jaula onde os container compartilham do mesmo namespace e do mesmo IP do POD
    
    ```bash
    
    #--image=''--vai baixar uma imagem la do dockerhub
    
    # -it - criar o pod e entrar diretamente nele
    
    # -rm - logo apos de executar o exit, o pod sera removido
    
    kubectl run --image alpine:3.14 --rm -it nomedopod sh
    ```
    
    **Fazer um redirecionamento de configuraçao yaml terraform para dentro de um arquivo .yaml**
    
    ```bash
    
    kubectl run --image hashicorp/terraform:latest terraform-demo --dry-run -oyaml | kubectl neat > terraform.yaml
    ```
    
    **Executar o SH do terraform**
    
    ```
    kubectl exec -it <nomePod> sh
    ```
    
    ### Init Container
    
    - Init container, sao containers que nao fazem parte do processo principal do pod
    - Sao containers que sao usados apenas na inicializaçao
    
    Em Kubernetes, um Init Container (contêiner de inicialização) é um tipo especial de contêiner que é executado antes do contêiner principal de um pod. Sua finalidade é realizar tarefas de inicialização, configuração ou preparação necessárias antes que o contêiner principal seja executado.
    
    Os Init Containers são úteis em situações em que é necessário que certas condições sejam atendidas antes que o aplicativo principal seja iniciado. Por exemplo, um Init Container pode ser usado para realizar as seguintes tarefas:
    
    1. **Configuração de volumes:** Um Init Container pode criar ou configurar volumes compartilhados que serão usados pelo contêiner principal. Ele pode, por exemplo, montar um volume de configuração ou realizar a cópia de arquivos para o volume.
    2. Inicialização de banco de dados: Se o seu aplicativo depende de um banco de dados, um Init Container pode ser usado para aguardar que o banco de dados esteja pronto antes de iniciar o aplicativo principal.
    3. Configuração de segredos: Um Init Container pode buscar segredos de um serviço externo ou de um local seguro, como um serviço de gerenciamento de segredos, e fornecê-los ao contêiner principal.
    4. Realização de verificações de integridade: Um Init Container pode ser usado para executar testes de integridade ou verificar a disponibilidade de serviços externos antes de iniciar o contêiner principal.
    5. Configuração de variáveis de ambiente: O Init Container pode definir variáveis de ambiente que serão usadas pelo contêiner principal, fornecendo configurações ou informações necessárias.
    
    Esses são apenas alguns exemplos de casos de uso para Init Containers. Eles fornecem uma maneira flexível de executar tarefas de inicialização e garantir que as dependências estejam satisfeitas antes de iniciar o contêiner principal de um pod no Kubernetes.
    
    ***Usos comuns:***
    
    - Esperar ate uma URL DNS estar disponivel
    - Codar um repo git
    - Açoes que devem ser tomadas antes de inicializar uma aplicaçao
    
    **Rodando o busybox**
    
    ```bash
    
    kubectl run --image busybox:1.28 --rm -it busybox-demo sh
    ```
    
    ### Nota 📋
    
    O BusyBox é uma ferramenta de linha de comando que fornece um conjunto de utilitários Unix em um único executável compacto. É projetado para ser executado em sistemas com recursos limitados, como sistemas embarcados, dispositivos IoT ou em ambientes com restrições de espaço.
    
    O BusyBox combina várias funcionalidades encontradas em comandos Unix, como ls, cp, mv, cat, grep, entre outros, em um único binário. Ele fornece uma maneira conveniente de acessar e executar esses utilitários em um ambiente com recursos restritos.
    
    Além disso, o BusyBox pode ser usado como um contêiner leve em ambientes de contêinerização, como o Docker. Ele é frequentemente usado como um contêiner de utilitários em contêineres Kubernetes, onde é executado como um contêiner auxiliar para executar tarefas de manutenção, depuração ou configuração em um pod.
    
    O BusyBox é flexível e altamente configurável, permitindo que você escolha quais utilitários incluir em sua compilação. Isso permite que você ajuste o tamanho e a funcionalidade do BusyBox de acordo com suas necessidades específicas.
    
    Em resumo, o BusyBox é uma ferramenta útil para ambientes com recursos limitados, fornecendo uma variedade de utilitários Unix em um único executável compacto.
    
    - um binario que vai conter varios binarios
    
    **Executar infinatamente no shell**
    
    ```bash
    
    until nslookup mymysql; do echo "Trying to resolve..."; sleep 1; done
    ```
    
    **Criando e executando um service para o mymysql**
    
    ```
    
    kubectl create service clusterip mymysql --tcp=80:80
    
    ```
    
    **Editando arquivo .yaml para rodar um initContainers, rodando imagem busybox**
    
    ```yaml
    
    apiVersion: v1
    kind: Pod
    metadata:
    	name: nginx-cd
    	namespace: default
    spec:
    	containers:
    	- name: nginx
          image: nginx:1.14.2
          ports:
          - containerPort: 80
    	initContainers:
        - name: waitfordns
        image: busybox:1.28
        command:
    		- sh
    		- -c
    		- until nslookup mymysql; do echo "Trying to resolve..."; sleep 1; done
    
    ```
    
    **Comando para vizualizar logs dentro de um init containers**
    
    ```
    kubectl logs nginx-cd -c waitfordns
    # A flag -c signica qual container queremos ver o log
    
    ```
    
    No contexto do Kubernetes, um multicontainer é uma abordagem na qual você empacota e executa vários contêineres relacionados como uma unidade lógica dentro de um pod. Um pod é a menor unidade de implantação no Kubernetes e pode conter um ou mais contêineres.
    
    Existem várias razões pelas quais você pode querer usar um multicontainer em Kubernetes. Aqui estão alguns casos de uso comuns:
    
    1. **Sidecar containers:** Um sidecar container é um contêiner auxiliar que trabalha em conjunto com o contêiner principal dentro do mesmo pod. Ele fornece funcionalidades adicionais, como armazenamento em cache, logging, monitoramento, balanceamento de carga ou qualquer outra tarefa auxiliar necessária pelo contêiner principal.
    2. **Containers de logs ou métricas:** Você pode ter um contêiner específico dentro do pod dedicado à coleta e envio de logs ou métricas para uma ferramenta de monitoramento centralizada. Isso ajuda a manter uma separação clara de responsabilidades e facilita o gerenciamento desses componentes.
    3. **Containers de inicialização:** Às vezes, você pode precisar executar um contêiner adicional para realizar tarefas específicas de inicialização antes que o contêiner principal seja iniciado. Isso pode incluir tarefas de pré-configuração, migração de dados ou outras ações necessárias antes que o serviço principal esteja pronto para atender solicitações.
    
    Ao usar um multicontainer em Kubernetes, é importante considerar a comunicação entre os contêineres. Os contêineres dentro de um pod compartilham o mesmo espaço de rede e podem se comunicar entre si usando a interface de loopback ou acessando serviços expostos por outros contêineres no mesmo pod. Além disso, você pode usar volumes compartilhados para permitir que os contêineres acessem os mesmos arquivos ou diretórios.
    
    Para definir um multicontainer em Kubernetes, você precisa criar um arquivo de manifesto (por exemplo, um arquivo YAML) que descreva a configuração do pod, incluindo os contêineres, volumes e outras propriedades relevantes. Em seguida, você pode implantar o pod usando o comando `kubectl apply -f <arquivo_manifesto.yaml>`.
    
    Lembre-se de que o uso de multicontainers pode adicionar complexidade à sua configuração, então certifique-se de entender claramente os requisitos e as interações entre os contêineres antes de adotá-los em sua arquitetura Kubernetes.
    
    **Rodando comando dentro de um pod**
    
    ```
    kubectl exec <nomePod> -c <nomeContainer> ls
    
    ```
    
    **Erro: CrashLoopBackOff*** - E um erro de aplicacao, o pod esta tentando subir mas nao consegue, e fica em loop tentando subir
    
    ### troubleshooting: Acessando um container de um pod sem o "kubectl exec"
    
    1. Entrar dentro do SSH Vagrant worker-1
    
    ### Static Pods
    
    - Sao pods que nao sao gerenciados pelo kubelet, sao gerenciados pelo kube-apiserver
    - Sao criados diretamente no worker node
    - Sao criados no diretorio /etc/kubernetes/manifests
    - Sao criados quando o kubelet inicia
    - Sao criados quando o kubelet detecta que um pod nao esta rodando
    
    Os Static Pods são uma forma de criar e gerenciar pods no Kubernetes. Ao contrário dos pods tradicionais, que são gerenciados pelo kubelet através do control plane do Kubernetes, os Static Pods são criados e gerenciados diretamente por um kubelet em um nó específico.
    
    Quando um kubelet é configurado para hospedar Static Pods, ele monitora um diretório local em busca de arquivos de manifesto de pod. Cada arquivo de manifesto representa um Static Pod e contém a definição YAML ou JSON do pod. O kubelet lê esses arquivos e cria os pods correspondentes no nó em que está sendo executado. Se o kubelet detectar alterações nos arquivos de manifesto, ele atualiza os pods em conformidade.
    
    Existem algumas características importantes dos Static Pods:
    
    1. **Escopo de nó:** Os Static Pods estão vinculados a um nó específico no cluster Kubernetes. Eles são criados apenas no nó em que o kubelet está configurado para hospedá-los. Essa abordagem é útil quando você deseja ter pods específicos em um nó sem precisar criar um objeto de pod tradicional no control plane do Kubernetes.
    2. Sem reconciliação automática: Diferentemente dos pods gerenciados pelo control plane do Kubernetes, os Static Pods não são reconciliados automaticamente se falharem ou forem excluídos. Se um Static Pod falhar, o kubelet não tentará reiniciá-lo automaticamente. Você precisará gerenciar manualmente os Static Pods para garantir que eles estejam sempre em execução.
    3. Integração limitada com recursos avançados do Kubernetes: Os Static Pods não têm todos os recursos e integrações disponíveis para os pods gerenciados pelo control plane do Kubernetes. Eles não são visíveis através do API Server e não podem ser escalados, atualizados ou gerenciados por controladores de replicação ou conjuntos de replicação. Os Static Pods são mais adequados para casos de uso simples e específicos em que esses recursos avançados não são necessários.
    
    Os Static Pods podem ser úteis em cenários específicos, como a execução de serviços de infraestrutura essenciais no nó do Kubernetes, onde você deseja que esses componentes sejam iniciados e executados antes mesmo do cluster estar totalmente configurado. No entanto, para a maioria dos casos de uso, é recomendável usar os pods gerenciados pelo control plane do Kubernetes, pois eles oferecem maior flexibilidade e recursos avançados de gerenciamento.
    
    **Comando para deletar todos os Pods**
    
    ```
    kubectl delete pods --all
    
    ```
    
    ### Rodando um pod estaticamente
    
    1. Entrar dentro do SSH Vagrant worker-1 ou worker-2
    2. Entrar no diretorio /etc/kubernetes/manifests
    3. Criar um arquivo chamado pod.yaml
    4. Adicionar o seguinte conteudo:
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: multicontainer-pod
      name: multicontainer-pod
    spec:
      containers:
      - image: httpd
        name: httpd
      - image: alpine
        name: debug
        command:
          - "sleep"
          - "999999999999"
    
    ```
    
    O pod sera criado automaticamente
    
    **OBS!!!** Quando um Pod e deletado e o arquivo de manifesto nao e deletado, o pod e criado novamente, e preciso deletar o arquivo de manifesto para que o pod nao seja criado novamente
    
    ### lifecycle: Como um pod é terminado?
    
    Um pod no Kubernetes pode ser terminado de várias maneiras, dependendo da situação e das ações tomadas. Aqui estão algumas das principais formas pelas quais um pod pode ser terminado:
    
    1. Encerramento voluntário: Um pod pode ser encerrado voluntariamente quando um aplicativo dentro do pod conclui sua tarefa ou é explicitamente encerrado. Isso pode acontecer quando o processo principal dentro do contêiner é encerrado ou quando um comando de encerramento é enviado para o contêiner. O pod passa do estado "Running" para o estado "Terminated" e é finalizado.
    2. Erro no contêiner: Se um contêiner dentro do pod encontrar um erro crítico ou um estado de falha que não pode ser recuperado, o Kubernetes encerrará o pod. Isso pode ocorrer se o contêiner principal falhar ao iniciar corretamente, se um health check (verificação de saúde) falhar repetidamente ou se ocorrer uma falha irreparável no aplicativo dentro do contêiner. O pod passa do estado "Running" para o estado "Terminated".
    3. Exclusão forçada: Um pod pode ser excluído forçadamente pelo usuário ou pelo Kubernetes. Isso pode ser feito usando o comando `kubectl delete pod <nome-do-pod>` ou por meio de ações administrativas. Quando um pod é excluído forçadamente, ele passa do estado "Running" para o estado "Terminating" e, em seguida, para o estado "Terminated". O tempo necessário para a exclusão pode variar dependendo de vários fatores, como o tempo de encerramento do contêiner e as políticas de encerramento configuradas.
    4. Escalonamento automático: Se o escalonamento automático estiver habilitado no Kubernetes e os recursos do cluster estiverem esgotados, o Kubernetes pode tomar a decisão de encerrar pods existentes para liberar recursos para outros pods em execução. Nesse caso, o pod passa pelo processo normal de encerramento descrito acima.
    
    É importante notar que, quando um pod é terminado, ele não é automaticamente reiniciado, a menos que seja gerenciado por um controlador de replicação ou um conjunto de replicação que defina uma política de reinicialização. O Kubernetes não reiniciará automaticamente pods que foram encerrados, a menos que seja especificamente instruído a fazê-lo.
    
    ```yaml
    
    apiVersion: v1
    kind: Pod
    metadata:
    	labels:
    		run: worker-pod
    		name: worker-pod
    spec:
    # definindo o tempo de vida do pod
    # tempo de vida do pod / intervalo de tempo para terminar o pod
    terminationGracePeriodSeconds: 60
      containers:
      - image: alpine
        name: alpine
        command:
          - "sleep"
          - "9999999999"
        lifecycle:
          # executa o comando antes de terminar o pod
          preStop:
            exec:
              command:
                - sh
                - -c
                - curl 10.244.2.24
    
    ```
    
    Ver logs do pod httpd
    
    ```
    kubectl logs -f httpd
    
    ```
  
    ## Pods-2
    
    ## **Primeiros passos - parte 1**
    
    - O POD é a menor unidade do kubernetes
    
    ```bash
    kubeadm token create --print-join-comand
    ```
    
    - Criar um novo token de autenticação que permite que um nó se junte a um cluster Kubernetes existente.
    
    ```bash
    
    kubectl get pods
    
    kubectl get pods -n kube-system
    
    kubectl describe pods -n kube-system <pod>
    
    kubectl get pods --all-namespaces -o wide
    
    kubectl get namespaces
    
    # Criando namespace com o nome giropops
    kubectl create namespace giropops
    
    # Criando um pod
    kubectl run nginx --image=nginx
    
    # Pegar o yaml de um objeto
    kubectl get pods nginx -o yaml
    # -o wide -> para ver mais detalhes
    
    # Jogando configuração do onbjeto para dentro de um arquivo yaml
    kubectl get pods nginx -o yaml > meu_primeiro_pod.yaml
    
    ```
    
    ### Criando um pod Yaml
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        run: nginx
      name: nginx
      namespace: giropops
    spec:
      containers:
      - image: nginx
      # definir a política de pull da imagem do contêiner.
        imagePullPolicy: Always
        name: nginx
        # define a política de DNS do contêiner.
        dnsPolicy: ClusterFirst
        # define a política de reinicialização do contêiner.
        restartPolicy: Always
    ```
    
    **Crie um Alias kubectl no seu .bashrc ou .zshrc**
    
    ```bash
    alias k='kubectl'
    ```
    
    **Vendo o POD rondando no namespace giropops**
    
    ```bash
    kubectl get po -n giropops
    
    kubectl run nginx --image=image --dry-run=client
    
    # O --dry-run=client ele finge que vai criar algo
    
    kubectl run nginx --image=nginx --dry-run=client -o yaml
    kubectl run nginx --image=nginx --dry-run=client -o yaml > meu_segundo_pod.yaml
    ```
    
    ## Primeiros passos - parte 2
    
    ```bash
    kubectl expose pod nginx
    ```
    
    O comando `kubectl expose pod nginx` é usado para criar um serviço que expõe o pod `nginx` na rede. Isso permite que outros pods dentro do cluster acessem o pod `nginx` usando o nome do serviço em vez do endereço IP do pod.
    
    Por padrão, o serviço criado por esse comando usa o protocolo TCP e seleciona automaticamente uma porta disponível para expor o pod. No entanto, você pode especificar o protocolo e a porta usando as opções `--protocol` e `--port`, respectivamente.
    
    Por exemplo, o comando `kubectl expose pod nginx --port=80 --protocol=TCP` criaria um serviço que expõe o pod `nginx` na porta 80 usando o protocolo TCP.
    
    ```bash
    # Pegando informação do meu service
    kubectl describe services nginx
    
    kubectl edit service nginx
    ```
    
    **Escalando deployment:**
    
    ```bash
    kubectl scale --replica=10 deployment nginx
    ```
    
    **Rodando uma porta no pod**
    
    ```bash
    kubectl create deployment --image=nginx nginx --port=80
    ```
    
    **Expondo um deployment** 
    
    ```bash
    kubectl expose deployment niginx
    
    kubectl expose deployment nginx --type=NodePorte
    ```
    
- **Deployments**
    
    ## Deployments
    
    ### O que é um Deployment?
    
    Um Deployment é um objeto do Kubernetes que gerencia um conjunto de objetos Pod.
    
    Como o Pod é a menor unidade de implantação no Kubernetes, o Deployment é a maneira mais comum de gerenciar Pods.
    
    **Comando para criar um deployment**
    
    ```bash
    
    kubectl create deployment nginx --image=nginx
    
    kubectl create deployment nginx --image=nginx --dry-run
    
    kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > nginx-deployment.yaml
    
    # Criando três réplicas
    kubectl create deployment nginx --image=nginx --replicas=3
    ```
    
    **Mostrar as labels de um determinado Pod**
    
    ```bash
    
    kubectl get pods --show-labels
    ```
    
    **Pegar pod a partir das labels**
    
    ```bash
    
    kubectl get pods -l app=nginx
    ```
    
    **Deletar pods a partir de labels**
    
    ```bash
    
    kubectl delete pods -l app=nginx
    kubectl delete pods -l k8s-app=kube-dns -n kube-system
    ```
    
    ### kubectl explain
    
    **O que e o kubectl explain e para que serve**
    
    O kubectl explain é um comando que mostra detalhes sobre um determinado objeto
    
    ```bash
    kubectl explain pods
    
    ```
    
    **Mostrar detalhes de um determinado objeto**
    
    ```bash
    kubectl explain pods.spec
    
    ```
    
    ### Grenciando Rollout
    
    **O que e Rollout?**
    
    O termo "Rollout" em Kubernetes refere-se a uma operação de atualização de implantação de um aplicativo ou serviço em um cluster Kubernetes. O Kubernetes oferece várias estratégias para atualizar aplicações de maneira controlada e sem interrupções para os usuários finais. Uma dessas estratégias é o "Rolling Update" (Atualização Rolante), que é frequentemente chamado de "Rollout."
    
    Um "Rollout" envolve a atualização gradual de pods (instancias de contêineres) em uma implantação para garantir que a nova versão do aplicativo seja implantada de forma segura e sem interrupções. Durante o processo de Rollout, o Kubernetes gerencia automaticamente a substituição dos pods da versão anterior pelos pods da nova versão. Isso pode ser feito de forma controlada, garantindo que um número mínimo de pods esteja sempre em execução e saudável antes de destruir os pods da versão anterior.
    
    O Kubernetes oferece várias maneiras de realizar um Rollout, incluindo estratégias como RollingUpdate, Blue-Green Deployment e Canary Deployment. Cada uma dessas estratégias tem suas próprias características e casos de uso específicos.
    
    No contexto do Kubernetes, um Rollout é uma operação essencial para garantir a disponibilidade e confiabilidade contínuas de seus aplicativos, permitindo que você atualize-os de maneira controlada e segura.
    
    **Comando Rollout**
    
    ```bash
    
    # Serve para mostrar o histórico de um determinado deployment
    
    kubectl rollout history deployment nginx-deployment
    
    kubectl rollout pause deployment nginx-deployment # Pausa o deployment
    
    kubectl rollout resume deployment nginx-deployment # Despausa o deployment
    
    kubectl rollout undo deployment nginx-deployment # Desfaz a ultima alteração
    
    kubectl rollout restart deployment nginx-deployment # Reinicia o deployment
    
    kubectl rollout status deployment nginx-deployment # Mostra o status do deployment
    
    kubectl rollout undo deployment/httpd --to-revision=1 # Desfaz o deployment para uma determinada revisão
    
    ```
    
    ### maxSurge e maxUnavailable: Otimizando deployment
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: httpd
      name: httpd
    spec:
    # A estrategia de atualizacao do deployment pode ser definida com o campo strategy
      strategy:
        rollingUpdate:
          maxSurge: 10%
          maxUnavailable: 0
      replicas: 100
      selector:
        matchLabels:
          app: httpd
      template:
        metadata:
          creationTimestamp: null
          labels:
            app: httpd
        spec:
          containers:
          - image: httpd
            name: httpd
    
    ```
    
    ```yaml
    
    strategy:
        rollingUpdate:
          maxSurge: 10%
          maxUnavailable: 0
    
    ```
    
    Esse trecho faz parte da definição de estratégia de atualização de um Deployment no Kubernetes. Nesse caso, a estratégia de atualização configurada é uma "RollingUpdate." Vou explicar o que cada parte dessa estratégia faz:
    
    1. `maxSurge`: Isso determina o número máximo de pods adicionais que podem ser criados acima do número desejado de réplicas durante uma atualização. No seu caso, está configurado como "10%". Isso significa que durante uma atualização, o Kubernetes permitirá que até 10% a mais de pods sejam criados temporariamente, além do número desejado de réplicas. Isso pode ser útil para garantir uma atualização rápida e suave, onde novas réplicas são implantadas antes que as antigas sejam removidas.
    2. `maxUnavailable`: Isso determina o número máximo de pods que podem ficar indisponíveis durante uma atualização. No seu caso, está configurado como "0". Isso significa que o Kubernetes garantirá que não haja pods indisponíveis durante a atualização. O Kubernetes irá garantir que, a qualquer momento, pelo menos o número desejado de réplicas do aplicativo esteja em execução e funcionando antes de desligar as réplicas antigas. Essa configuração é útil para garantir alta disponibilidade durante a atualização.
    
    Portanto, com essa configuração específica, o Kubernetes está garantindo que:
    
    - Durante uma atualização, até 10% a mais de pods podem ser implantados temporariamente (maxSurge) para acelerar a atualização.
    - Não haverá pods indisponíveis (maxUnavailable: 0) durante a atualização, garantindo alta disponibilidade.
    
    Essas configurações podem ser ajustadas de acordo com os requisitos específicos de disponibilidade e tolerância a falhas do seu aplicativo.
    
    ### Escalando um deployment
    
    - Nao e recomendado escalar deployments
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: httpd
      name: httpd
    spec:
    # A estrategia de atualizacao do deployment pode ser definida com o campo strategy
      strategy:
        rollingUpdate:
          maxSurge: 10%
          maxUnavailable: 0
      replicas: 50
      selector:
        matchLabels:
          app: httpd
      template:
        metadata:
          creationTimestamp: null
          labels:
            app: httpd
        spec:
          containers:
          - image: httpd
            name: httpd
    
    ```
    
    **Definir um novo numero de replicas com o Scale**
    
    ```
    	kubectl scale deployment httpd --replicas 110
    
    ```
    
    ### Resources: Como delimitar recursos computacionais?
    
    ![Pasted image 20231019161940.png](Kubernetes%20730e37146baa485d9f46957707434f50/Pasted_image_20231019161940.png)
    
    ![Pasted image 20231019162037.png](Kubernetes%20730e37146baa485d9f46957707434f50/Pasted_image_20231019162037.png)
    
    Em Kubernetes, você pode limitar os recursos computacionais (CPU e memória) que um contêiner pode usar em um pod. Isso é importante para garantir que os recursos do cluster sejam alocados de forma eficiente e que um pod mal comportado não prejudique outros pods no mesmo nó. Para definir limites de recursos, você pode usar os campos `resources.limits` e `resources.requests` no arquivo de especificação do pod. Aqui está como você pode fazê-lo:
    
    1. **Limites (Limits)**: Os limites são a quantidade máxima de recursos que um pod pode usar. Se um pod tentar usar mais recursos do que o limite especificado, ele será limitado a esse valor. Isso é útil para garantir que um pod não sobrecarregue o nó e cause problemas para outros pods. Para definir limites, você pode adicionar o seguinte ao arquivo de especificação do pod:
        
        ```yaml
        resources:
          limits:
            memory: 512Mi
            cpu: 500m
        
        ```
        
        Neste exemplo, o pod tem um limite de 512 megabytes de memória e 500 milicores de CPU (equivalente a 0,5 núcleos).
        
    2. **Requisições (Requests)**: As requisições são a quantidade de recursos que um pod solicita ao cluster. O Kubernetes tentará alocar pelo menos essa quantidade de recursos no nó em que o pod será executado. Isso é útil para garantir que o pod tenha recursos mínimos garantidos. Para definir requisições, você pode adicionar o seguinte ao arquivo de especificação do pod:
        
        ```yaml
        resources:
          requests:
            memory: 256Mi
            cpu: 250m
        
        ```
        
        Neste exemplo, o pod solicita 256 megabytes de memória e 250 milicores de CPU.
        
    3. **Exemplo Completo**:
        
        Aqui está um exemplo mais completo de como definir limites e requisições de recursos em um arquivo de especificação de pod:
        
        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          name: meu-pod
        spec:
          containers:
            - name: meu-container
              image: minha-imagem:latest
              resources:
                limits:
                  memory: 512Mi
                  cpu: 500m
                requests:
                  memory: 256Mi
                  cpu: 250m
        
        ```
        
    
    Lembre-se de que essas são apenas diretrizes e os valores exatos de limites e requisições devem ser ajustados de acordo com as necessidades específicas de seus aplicativos. Monitorar o desempenho do cluster é fundamental para garantir que os recursos sejam alocados de maneira eficiente e que os pods estejam recebendo os recursos de que precisam.
    
    ### request & limits na prática
    
    - Os resources sao especificos de cada container
    
    **Comando para saber mais sobre os resources com o explain**
    
    ```
    kubectl explain deployment.spec.template.spec.containers.resources
    
    ```
    
    **Comando para verificar a quantidade de memoria de um no**
    
    ```
    kubectl top node
    
    ```
    
    **Aplicando o requests e limits**
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: httpd
      name: httpd
    spec:
    # A estrategia de atualizacao do deployment pode ser definida com o campo strategy
      strategy:
        rollingUpdate:
          maxSurge: 10%
          maxUnavailable: 0
      replicas: 1
      selector:
        matchLabels:
          app: httpd
      template:
        metadata:
          creationTimestamp: null
          labels:
            app: httpd
        spec:
          containers:
          - image: httpd
            name: httpd
            resources:
              requests:
                cpu: 100m
                memory: 512M
              limits:
                cpu: 10
                memory: 8192M
    
    ```
    
    ### goldilocks: Como descobrir o request/limits ideal para a aplicação
    
    - Goldilocks é uma ferramenta fantástica para definir o request/limits ideal para a aplicação.
    
    O "Goldilocks" é uma ferramenta de código aberto criada para o Kubernetes que ajuda a otimizar os recursos de CPU e memória alocados para os pods em um cluster Kubernetes. O objetivo principal do Goldilocks é fornecer recomendações sobre os limites e requisições de recursos apropriados para os pods, com base em métricas de uso de recursos em tempo real.
    
    Aqui está para que o Goldilocks Kubernetes é usado:
    
    1. **Otimização de Recursos**: O Kubernetes permite que você defina limites e requisições de recursos para pods, mas determinar os valores ideais pode ser desafiador. Se você alocar recursos em excesso, pode desperdiçar recursos valiosos. Se alocar muito pouco, os pods podem enfrentar problemas de desempenho. O Goldilocks ajuda a encontrar o equilíbrio certo, otimizando os recursos para maximizar a eficiência.
    2. **Recomendações Dinâmicas**: Em vez de confiar apenas em valores estáticos definidos nos arquivos de especificação do pod, o Goldilocks monitora o uso real de CPU e memória pelos pods em execução e fornece recomendações dinâmicas com base em métricas observadas.
    3. **Aprimoramento do Desempenho**: Ao garantir que os pods tenham recursos apropriados, o Goldilocks pode ajudar a evitar problemas de desempenho devido a limites muito baixos e reduzir a possibilidade de contenção de recursos em um cluster.
    4. **Economia de Recursos**: A otimização de recursos com o Goldilocks pode economizar dinheiro, especialmente quando você está usando serviços de nuvem que cobram com base nos recursos alocados.
    5. **Integração com Prometheus**: O Goldilocks é frequentemente usado em conjunto com o Prometheus, um sistema de monitoramento amplamente utilizado no ecossistema Kubernetes. Ele coleta métricas do Prometheus e usa esses dados para gerar recomendações.
    6. **Facilita a Manutenção**: Manter as configurações de recursos dos pods atualizadas pode ser um desafio, especialmente em clusters de grande escala. O Goldilocks automatiza parte desse processo, tornando a manutenção mais eficiente.
    
    Em resumo, o Goldilocks é uma ferramenta que ajuda a garantir que os recursos em um cluster Kubernetes sejam alocados de maneira eficiente, maximizando o desempenho e minimizando o desperdício de recursos. Isso é particularmente útil em ambientes de produção onde é essencial otimizar a utilização de recursos.
    
    - **[Guia de instalação do Goldilocks](https://goldilocks.docs.fairwinds.com/installation/#requirements)**
    
    Outros links interessantes usados na aula:
    
    - **[Download do Helm](https://helm.sh/docs/intro/install/)**
    - **[Vertical Pod Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler)**
    - **[Values do Helm Chart do Goldilocks](https://artifacthub.io/packages/helm/fairwinds-stable/goldilocks?modal=values)**
    
    **Segue o link para instalar o Goldilocks: [Aqui](https://artifacthub.io/packages/helm/fairwinds-stable/goldilocks)**
    
    - crie um arquivo values.yaml e implemente o seguinte codigo
    
    ```yaml
    
    # uninstallVPA -- Enabling this flag will remove a vpa installation that was previously managed with this chart. It is considered deprecated and will be removed in a later release.
    uninstallVPA: false
    
    vpa:
      # vpa.enabled -- If true, the vpa will be installed as a sub-chart
      enabled: true
      updater:
        enabled: false
    
    metrics-server:
      # metrics-server.enabled -- If true, the metrics-server will be installed as a sub-chart
      enabled: true
      apiService:
        create: true
    
    image:
      # image.repository -- Repository for the goldilocks image
      repository: us-docker.pkg.dev/fairwinds-ops/oss/goldilocks
      # image.tag -- The goldilocks image tag to use
      tag: v4.10.0
      # image.pullPolicy -- imagePullPolicy - Highly recommended to leave this as `Always`
      pullPolicy: Always
    
    # imagePullSecrets -- A list of image pull secret names to use
    imagePullSecrets: []
    
    nameOverride: ""
    fullnameOverride: ""
    
    controller:
      # controller.enabled -- Whether or not to install the controller deployment
      enabled: true
      # controller.revisionHistoryLimit -- Number of old replicasets to retain, default is 10, 0 will garbage-collect old replicasets
      revisionHistoryLimit: 10
      rbac:
        # controller.rbac.create -- If set to true, rbac resources will be created for the controller
        create: true
        # controller.rbac.enableArgoproj -- If set to true, the clusterrole will give access to argoproj.io resources
        enableArgoproj: true
        # controller.rbac.extraRules -- Extra rbac rules for the controller clusterrole
        extraRules: []
        # controller.rbac.extraClusterRoleBindings -- A list of ClusterRoles for which ClusterRoleBindings will be created for the ServiceAccount, if enabled
        extraClusterRoleBindings: []
      serviceAccount:
        # controller.serviceAccount.create -- If true, a service account will be created for the controller. If set to false, you must set `controller.serviceAccount.name`
        create: true
        # controller.serviceAccount.name -- The name of an existing service account to use for the controller. Combined with `controller.serviceAccount.create`
        name:
    
      # controller.flags -- A map of additional flags to pass to the controller
      flags: {}
      # controller.logVerbosity -- Controller log verbosity. Can be set from 1-10 with 10 being extremely verbose
      logVerbosity: "2"
      # controller.nodeSelector -- Node selector for the controller pod
      nodeSelector: {}
      # controller.tolerations -- Tolerations for the controller pod
      tolerations: []
      # controller.affinity -- Affinity for the controller pods
      affinity: {}
      # controller.topologySpreadConstraints -- Topology spread constraints for the controller pods
      topologySpreadConstraints: []
      # controller.resources -- The resources block for the controller pods
      resources:
        limits: {}
        requests:
          cpu: 25m
          memory: 256Mi
      # controller.podSecurityContext -- Defines the podSecurityContext for the controller pod
      podSecurityContext:
        seccompProfile:
          type: RuntimeDefault
      # controller.securityContext -- The container securityContext for the controller container
      securityContext:
        readOnlyRootFilesystem: true
        allowPrivilegeEscalation: false
        runAsNonRoot: true
        runAsUser: 10324
        capabilities:
          drop:
            - ALL
    
      deployment:
        # controller.deployment.extraVolumeMounts -- Extra volume mounts for the controller container
        extraVolumeMounts: []
        # controller.deployment.extraVolumes -- Extra volumes for the controller pod
        extraVolumes: []
        # controller.deployment.annotations -- Extra annotations for the controller deployment
        annotations: {}
        # controller.deployment.additionalLabels -- Extra labels for the controller deployment
        additionalLabels: {}
    
        # controller.deployment.podAnnotations -- Extra annotations for the controller pod
        podAnnotations: {}
    
    dashboard:
      # dashboard.basePath -- Path on which the dashboard is served. Defaults to `/`
      basePath: null
      # dashboard.enabled -- If true, the dashboard component will be installed
      enabled: true
      # dashboard.revisionHistoryLimit -- Number of old replicasets to retain, default is 10, 0 will garbage-collect old replicasets
      revisionHistoryLimit: 10
      # dashboard.replicaCount -- Number of dashboard pods to run
      replicaCount: 2
      service:
        # dashboard.service.type -- The type of the dashboard service
        type: ClusterIP
        # dashboard.service.port -- The port to run the dashboard service on
        port: 80
        # dashboard.service.annotations -- Extra annotations for the dashboard service
        annotations: {}
      # dashboard.flags -- A map of additional flags to pass to the dashboard
      flags: {}
      # dashboard.logVerbosity -- Dashboard log verbosity. Can be set from 1-10 with 10 being extremely verbose
      logVerbosity: "2"
      # dashboard.excludeContainers -- Container names to exclude from displaying in the Goldilocks dashboard
      excludeContainers: "linkerd-proxy,istio-proxy"
      rbac:
        # dashboard.rbac.create -- If set to true, rbac resources will be created for the dashboard
        create: true
        # dashboard.rbac.enableArgoproj -- If set to true, the clusterrole will give access to argoproj.io resources
        enableArgoproj: true
      serviceAccount:
        # dashboard.serviceAccount.create -- If true, a service account will be created for the dashboard. If set to false, you must set `dashboard.serviceAccount.name`
        create: true
        # dashboard.serviceAccount.name -- The name of an existing service account to use for the controller. Combined with `dashboard.serviceAccount.create`
        name:
    
      deployment:
        # dashboard.deployment.annotations -- Extra annotations for the dashboard deployment
        annotations: {}
        # dashboard.deployment.additionalLabels -- Extra labels for the dashboard deployment
        additionalLabels: {}
        # dashboard.deployment.extraVolumeMounts -- Extra volume mounts for the dashboard container
        extraVolumeMounts: []
        # dashboard.deployment.extraVolumes -- Extra volumes for the dashboard pod
        extraVolumes: []
    
        # dashboard.deployment.podAnnotations -- Extra annotations for the dashboard pod
        podAnnotations: {}
    
      ingress:
        # dashboard.ingress.enabled -- Enables an ingress object for the dashboard.
        enabled: false
    
        # dashboard.ingress.ingressClassName -- From Kubernetes 1.18+ this field is supported in case your ingress controller supports it. When set, you do not need to add the ingress class as annotation.
        ingressClassName:
        annotations: {}
          # kubernetes.io/ingress.class: nginx
          # kubernetes.io/tls-acme: "true"
        hosts:
          - host: chart-example.local
            paths:
              - path: /
                type: ImplementationSpecific
    
        tls: []
        #  - secretName: chart-example-tls
        #    hosts:
        #      - chart-example.local
    
      # dashboard.resources -- A resources block for the dashboard.
      resources:
        limits: {}
        requests:
          cpu: 25m
          memory: 256Mi
      # dashboard.podSecurityContext -- Defines the podSecurityContext for the dashboard pod
      podSecurityContext:
        seccompProfile:
          type: RuntimeDefault
      # dashboard.securityContext -- The container securityContext for the dashboard container
      securityContext:
        readOnlyRootFilesystem: true
        allowPrivilegeEscalation: false
        runAsNonRoot: true
        runAsUser: 10324
        capabilities:
          drop:
            - ALL
      nodeSelector: {}
      tolerations: []
      affinity: {}
      # dashboard.topologySpreadConstraints -- Topology spread constraints for the dashboard pods
      topologySpreadConstraints: []
    
    ```
    
    - Coloque true no vpa e metrics-server em enabled
    - Instalando o goldilocks
    
    ```
    helm repo add fairwinds-stable <https://charts.fairwinds.com/stable>
    kubectl create namespace goldilocks
    Helm v2:
    helm install --name goldilocks --namespace goldilocks fairwinds-stable/goldilocks
    Helm v3:
    helm install goldilocks --namespace goldilocks fairwinds-stable/goldilocks
    
    ```
    
    ou
    
    ```
    helm install goldiloks --namespace goldilocks --create-namespace fairwinds-stable/goldilocks -f values.yaml
    
    ```
    
    ![Pasted image 20231020111105.png](Kubernetes%20730e37146baa485d9f46957707434f50/Pasted_image_20231020111105.png)
    
    ```yaml
    kubectl get po -n goldilocks
    
    ```
    
    Vai haver um erro no metricserver, pois estamos utilizando o kind
    
    - Para corrigir vai em:
    
    ```
    # 1
    kubectl get deploy -n goldilocks
    
    #2
    kubectl edit deploy -n goldilocks goldiloks-metrics-server
    
    ```
    
    - Depois edite o arquivo yaml em args e implemente o seguinte:
    
    ```yaml
    - args:
    	- --secure-port=8443
    	- --kubelet-insecure-tls
    
    ```
    
    ```
    kubectl logs -n goldilocks goldilocks-vpa-recomender-867d86856-lp6ct -f
    
    ```
    
    - **port-forward** - vai pegar um porta da maquina atual e vai criar um tunel ate o service dentro do cluster
    
    **Comando para vizualizar labels dos namespaces**
    
    ```
    kubectl get ns --show-labels
    
    ```
    
    **Escolha um namespace de aplicativo e rotule-o assim para ver alguns dados:**
    
    ```
    kubectl label ns goldilocks goldilocks.fairwinds.com/enabled=true
    
    ```
    
    **Vizualizar vpa criado para um determinado namespace:**
    
    ```
    kubectl get vpa -n application
    
    ```
    
    ```
    kubectl create deployment --image nginx nginx -n application --replicas 3
    
    ```
    
    ### Visualizando o Painel
    
    A instalação padrão cria um serviço ClusterIP para o painel. Você pode acessar via encaminhamento de porta:
    
    ```
    kubectl -n goldilocks port-forward svc/goldilocks-dashboard 8080:80
    
    ```
    
    Apos de dar um port-forward, digite a url: [http://127.0.0.1:8080/](http://127.0.0.1:8080/)
    
    ![Pasted image 20231020125630.png](Kubernetes%20730e37146baa485d9f46957707434f50/Pasted_image_20231020125630.png)
    
    ![Pasted image 20231020125728.png](Kubernetes%20730e37146baa485d9f46957707434f50/Pasted_image_20231020125728.png)
    
    ```
    kubectl describe vpa -n application goldilocks-http
    
    ```
    
    ### troubleshooting: Como analisar OOM (Out Of Memory) no cluster Kubernetes?
    
    O segredo aqui é acessar o Node que está hosteando o pod e procurar pelo **/var/log/messsages** ou **/var/log/kern.log**.
    
    Dentro do log, você terá uma mensagem parecida com essa aqui:
    
    **Nov  9 16:40:30 k8s-worker-2 kernel: [5189358.480218] Memory cgroup out of memory: Killed process 321757 (java) total-vm:3473912kB, anon-rss:972768kB, file-rss:0kB, shmem-rss:0kB, UID:0 pgtables:2072kB oom_score_adj:918**
    
    É importante se atentar ao anon-rss ou file-rss, que é o quanto foi lockado pelo processo da memória física mesmo. Você vai tirar alguns insights sobre o consumo e talvez ter uma ideia do que modificar.
    
    Além disso, sempre bom dar uma olhada no sistema de monitoramento por métricas de consumo de RAM para entender mais sobre o que ocorreu.
    
    Estou anexando o kern.log que usei na aula caso você queira dar uma olhada.
    
    ### Materiais complementares
    
    ![[kern.log]]
    
    **Yaml utilizado para teste:**
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        app: oom-deployment
      name: oom-deployment
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: oom-deployment
      template:
        metadata:
          creationTimestamp: null
          labels:
            app: oom-deployment
        spec:
          containers:
          - image: valentinomiazzo/jvm-memory-test
            name: oom-deployment
            env:
              - name: "ALLOC_HEAP_MB"
                value: "100"
              - name: "MAX_HEAP_SIZE_MB"
                value: "2000"
            resources:
              requests:
                cpu: 100m
                memory: 512M
              limits:
                cpu: 1
                memory: 1G
    
    ```
    
    O container nada mais do que um processo dentro do sistema operacional
    
    **Comandos para lista contexts e usar contexts (Clusters)**
    
    ```bash
    # listar
    kubectl config get-contexts
    
    # alterar cluster
    kubectl config use-context nome-do-contexto-desejado
    
    ```
    
    Entrar no modo SSH do no
    
    ```bash
    ssh -p 2222 ubuntu@localhost
    
    ```
    
  ## **Deployments - 2**
    
    ### Introdução
    
    - Um Deployment, nada mais é do que o controller do replicaset
    
    **YAML**
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    	name: primeiro-deployment
    	labels:
    		# Chave valor
    		run: nginx
    		app: giropops
    		namespace: default
    spec:
    	replicas: 1
    	selector:
    		# tem que se igualar a label do metadata do deployment passado a cima
    		# como se fosse uma herança
    		matchLabels:
    			run: nginx
    	template:
    		metadata:
    			labels:
    				run: nginx
    				# Entrada, como se tivesse rodando no datacenter do Reino Unido
    				dc: UK
    		spec:
    			containers:
    				- image: nginx
    					imagePullPolicy: Always
    					name: nginx
    					ports:
    					- containerPort: 80
    						protocol: TCP
    					resource: {}
    					terminationMessagePath: /dev/termination-log
             terminationMessagePolicy: File
          dnsPolicy: ClusterFirst
          restartPolicy: Always
          schedulerName: default-scheduler
          securityContext: {}
    					
    		
    
    	
    ```
    
    **Explicando cada linha do deployment:** 
    
    - `apiVersion: aps/v1`: Define a versão da API que está sendo usada para criar o recurso.
    - `kind: Deployment`: Especifica o tipo de recurso que está sendo criado. Neste caso, é um Deployment.
    - `metadata:`: Fornece dados adicionais sobre o recurso, como nome, namespace, labels e annotations.
        - `labels:`: São pares chave-valor usados para organizar e categorizar recursos.
            - `run: nginx`: Uma label com a chave 'run' e o valor 'nginx'.
            - `app: giropops`: Uma label com a chave 'app' e o valor 'giropops'.
        - `name: primeiro-deployment`: O nome do recurso.
        - `namespace: default`: O namespace onde o recurso será criado.
    - `spec:`: Define o estado desejado para o recurso.
        - `replicas: 3`: Especifica que três réplicas do pod devem ser mantidas em execução.
        - `selector:`: Define como o Deployment encontra quais Pods gerenciar.
            - `matchLabels:`: Uma forma de seleção que deve corresponder para que o pod seja selecionado.
                - `app: primeiro-deployment`: A label que deve corresponder.
        - `template:`: Define o template do pod que será usado para criar novos pods.
            - `metadata:`: Metadados para o Pod, incluindo labels e annotations.
                - `labels:`: Labels para o Pod.
                    - `app: primeiro-deployment`: Uma label para o Pod.
            - `spec:`: A especificação do Pod.
                - `containers:`: A lista de contêineres que serão executados no Pod.
                    - `name: primeiro-deployment`: O nome do contêiner.
                    - `image: nginx`: A imagem do contêiner que será usada.
                    - `imagePullPolicy: Always`: A política de obtenção de imagem. 'Always' significa que a imagem será sempre puxada.
                    - `ports:`: A lista de portas que serão abertas no contêiner.
                        - `containerPort: 80`: A porta que será aberta no contêiner.
                    - `protocol: TCP`: O protocolo que será usado para a porta.
                    - `resources: {}`: Os recursos computacionais que serão alocados para o contêiner.
                    - `terminationMessagePath: /dev/termination-log`: O caminho para o arquivo que armazenará a mensagem de término quando o contêiner parar.
                    - `terminationMessagePolicy: File`: A política para a mensagem de término. 'File' significa que a mensagem será escrita no arquivo especificado acima.
                - `dnsPolicy: ClusterFirst`: A política de DNS para o Pod.
                - `restartPolicy: Always`: A política de reinício para o Pod. 'Always' significa que o Pod será sempre reiniciado se parar.
                - `schedulerName: default-scheduler`: O nome do agendador que será usado para agendar o Pod.
                - `securityContext: {}`: Define as configurações de segurança para o Pod.
    
    ---
    
    ### Deployment, Label e Node Selector
    
    ```yaml
    kubectl create -f <arquivo.yaml>
    
    kubectl describe deployment <nomeDeployment>
    
    kubectl get replicaset
    
    kubectl describe replicaset <nome>
    
    # Pegar um pod com uma determinada label
    kubectl get pods -l dc=NL
    
    # Pegar um pod com uma determinada label e colocando uma coluna extra
    kubectl get pods -L dc
    
    kubectl get replicaset -l dc=NL
    kubectl get replicaset -L dc=UK
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%201.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%202.png)
    
    **Setando label e um determinado node**
    
    ```bash
    kubectl label nodes <nomeNode> disk=SSD
    kubectl label nodes <nomeNode> disk=HDD
    
    kubectl describe nodes <nomeNode>
    ```
    
    **Listar todos os Labels de um determinado objeto e reescrevendo label**
    
    ```bash
    kubectl label nodes <nomeNode> --list
    kubectl label nodes <nomeNode> app=nginx --overwrite
    ```
    
    **NodeSelector**
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      labels:
        run: nginx
        app: giropops
      name: terceiro-deployment
      namespace: default
    spec:
      replicas: 3
      # tem que se igualar a label do metadata do deployment passado a cima
      selector:
        matchLabels:
          run: nginx
      template:
        metadata:
          labels:
            run: nginx
            dc: UK
        spec:
          containers:
          - name: terceiro-deployment
            image: nginx
            imagePullPolicy: Always
            ports:
            - containerPort: 80
              protocol: TCP
            resources: {}
            terminationMessagePath: /dev/termination-log
            terminationMessagePolicy: File
          dnsPolicy: ClusterFirst
          restartPolicy: Always
          schedulerName: default-scheduler
          securityContext: {}
          # Vai selecionar o node que tiver o label disk: SSD
          #Senao tiver o label, nao vai subir o pod
          nodeSelector:
            disk: SSD
    ```
    
    ```yaml
    kubectl get pod -o wide
    kubectl describe nodes <nomeNode>
    ```
    
    ---
    
    ### Mais um pouco sobre Labels
    
    **Remover labels:**
    
    ```yaml
    kubectl label nodes <nomeLabel>- --all
    kubectl label node disk- --all
    ```
    
    ---
    
    ### Replicaset
    
    Um ReplicaSet no Kubernetes é um objeto que garante que um número especificado de réplicas de um pod estejam sempre em execução. Em outras palavras, um ReplicaSet garante que um pod ou um conjunto homogêneo de pods estejam sempre disponíveis e em execução.
    
    O ReplicaSet é frequentemente usado para garantir a disponibilidade de um número especificado de Pods idênticos. Se os pods estiverem morrendo ou sendo excluídos, o ReplicaSet garante que novos pods sejam criados para substituí-los e manter o número desejado de pods.
    
    Aqui está um exemplo de como um ReplicaSet pode ser definido em um arquivo YAML:
    
    ```yaml
    apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: exemplo-replicaset
      labels:
        app: exemplo-app
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: exemplo-app
      template:
        metadata:
          labels:
            app: exemplo-app
        spec:
          containers:
          - name: exemplo-container
            image: exemplo-imagem
    
    ```
    
    Neste exemplo, o ReplicaSet garante que sempre haja três réplicas do pod em execução. Se um pod falhar, o ReplicaSet criará um novo pod para substituí-lo.
    
    **Utilizando algumas nomenclatura dos objetos Kubernetes**
    
    ```bash
    #Replicaset
    kubectl get rs 
    #Pod
    kubectl get po
    #Deployment
    kubectl get deploy
    #Service
    kubectl get svc
    #Endpoint
    kubectl get ep
    ```
    
    ```bash
    kubectl describe rs <nomeRS>
    kubectl edit rs <nomeRs>
    ```
    
    OBS! O Replicaset, a função é ser um controlador de pods
    
    ---
    
    ### Daemonset
    
    - O DaemonSet é um objeto que garante que um conjunto de pods esteja em execução em todos os nodes do cluster.
    - Um Daemonset é praticamente um replicaset com a principal diferença é que não escolhe a quantas replicas o pod irá possuir.
    - Não define a quantidade de réplicas.
    - Vai ser utilizado quando é preciso rodar um pod em todos os nós do cluster.
    - Ex: Imagine. uma ferramenta que está coletando dados de todo o cluster, de todos os nós, e quando subir esse cara é preciso ter a garantia que o mesmo esteja coletando de todos os nós.
    
    **YAML do Deamonset:**
    
    ```yaml
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: nginx-ds
      labels:
        system: strigus
    spec:
      selector:
        matchLabels:
          system: strigus
      template:
        metadata:
          labels:
            system: strigus
        spec:
          containers:
            - name: nginx
              image: nginx:1.7.9
              ports:
                - containerPort: 80
    ```
    
    ```yaml
    kubectl create -f daemonset.yaml
    kubectl get ds
    ```
    
    **Removendo a chave do taint que está em NoSchedule**
    
    ```yaml
    kubectl taint node eliot-01 node-role.kubernetes.io/master-
    ```
    
    **Alterando yaml via linha de comando:**
    
    ```yaml
    # Alterando versão da imagem nginx pela linha de comando
    kubectl set image ds nginx-ds nginx=nginx:1.15.0
    ```
    
    **OBS!** Só vai mudar de versão assim que o pod morrer e automaticamente levantar outro.
    
    ---
    
    ### Rollouts e Rollbacks
    
    ```yaml
    kubectl rollout history daemonset nginx-ds
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%203.png)
    
    **Comando para mostrar mais detalhes de uma revisão**
    
    ```yaml
    kubectl rollout history daemonset nginx-ds --revision=1
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%204.png)
    
    ```yaml
    kubectl rollout history daemonset nginx-ds --revision=2
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%205.png)
    
    **Fazendo um Rollback**
    
    ```yaml
    kubectl rollout undo daemonset nginx-ds --to-revision=1
    ```
    
    **UpdateStrategy**
    
    ```yaml
    apiVersion: apps/v1
    kind: DaemonSet
    metadata:
      name: nginx-ds
      labels:
        system: strigus
    spec:
      selector:
        matchLabels:
          system: strigus
      template:
        metadata:
          labels:
            system: strigus
        spec:
          containers:
            - name: nginx
              image: nginx:1.7.9
              ports:
                - containerPort: 80
    	updateStrategy:
    	  type: RollingUpdate
    ```
    
    - É um estratégia de atualização definida como RollingUpdate, o que significa que a atualização será feita de forma gradual, um node de cada vez, para garantir que o DaemonSet esteja sempre em execução.
    
    ---
    
    ### Canary Deploy
    
    O Canary Deploy é uma técnica de implantação que permite testar novas versões de um aplicativo em um ambiente de produção com um subconjunto mínimo de usuários antes de fazer um lançamento completo.
    
    No contexto do Kubernetes, o Canary Deploy é útil para:
    
    1. **Reduzir o risco de problemas de produção:** Ao implantar a nova versão para um pequeno grupo de usuários primeiro, você pode identificar e corrigir problemas antes que eles afetem todos os usuários.
    2. **Obter feedback do usuário:** Você pode coletar feedback dos usuários do Canary Deploy para fazer melhorias antes do lançamento completo.
    3. **Facilitar reversões:** Se algo der errado com a nova versão, você pode reverter para a versão anterior com interrupção mínima para os usuários.
        
        
    
    Exemplo:
    
    1. Dê um git clone nesse projeto abaixo:
        
        ```bash
        git clone https://github.com/badtuxx/k8s-canary-deploy-example.git
        ```
        
    2. Agora faça o seguintes passos para implementar:
        
        ```bash
        cp k8s-canary-deploy-example/deploy-app-v1-playbook/roles/common/files/* .
        
        cp k8s-canary-deploy-example/canary-deploy-app-v2-playbook/roles/common/files/* .
        
        cp k8s-canary-deploy-example/deploy-app-v2-playbook/roles/common/files/* .
        
        ```
        
    3. YAML a serem implementado:
        
        Service: service-app.yaml
        
        ```yaml
        apiVersion: v1
        kind: Service
        metadata:
          labels:
            app: giropops
            run: nginx
            track: stable
          name: giropops
          namespace: default
        spec:
          externalTrafficPolicy: Cluster
          ports:
          - nodePort: 32222
            name: http
            port: 80
            protocol: TCP
            targetPort: 80
          - nodePort: 32111
            name: prometheus
            port: 32111
            protocol: TCP
            targetPort: 32111
          selector:
            app: giropops
          sessionAffinity: None
          type: NodePort
        ```
        
        Deployments: app-v1.yaml, app-v2-canary.yaml, app-v2.yaml
        
        ```yaml
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: giropops-v1
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                app: giropops
                version: "1.0.0"
              annotations:
                prometheus.io/scrape: "true"
                prometheus.io/port: "32111"
            spec:
              containers:
              - name: giropops
                image: linuxtips/nginx-prometheus-exporter:1.0.0
                env:
                - name: VERSION
                  value: "1.0.0"
                ports:
                - containerPort: 80
                - containerPort: 32111
        ```
        
        ```yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: giropops-v2
          labels:
            app: giropops
            version: "2.0.0"
        spec:
          replicas: 1
          selector:
            matchLabels:
              app: giropops
              version: "2.0.0"
          template:
            metadata:
              labels:
                app: giropops
                version: "2.0.0"
              annotations:
                prometheus.io/scrape: "true"
                prometheus.io/port: "32111"
            spec:
              containers:
              - name: giropops
                image: linuxtips/nginx-prometheus-exporter:2.0.0
                env:
                - name: VERSION
                  value: "2.0.0"
                ports:
                - containerPort: 80
                - containerPort: 32111
        ```
        
        ```yaml
        apiVersion: apps/v1
        kind: Deployment
        metadata:
          name: giropops-v2
          labels:
            app: giropops
            version: "2.0.0"
        spec:
          replicas: 10
          selector:
            matchLabels:
              app: giropops
              version: "2.0.0"
          template:
            metadata:
              labels:
                app: giropops
                version: "2.0.0"
              annotations:
                prometheus.io/scrape: "true"
                prometheus.io/port: "32111"
            spec:
              containers:
              - name: giropops
                image: linuxtips/nginx-prometheus-exporter:2.0.0
                env:
                - name: VERSION
                  value: "2.0.0"
                livenessProbe:
                  httpGet:
                    path: /
                    port: 80
                    scheme: HTTP
                readinessProbe:
                  httpGet:
                    path: /
                    port: 80
                    scheme: HTTP
                ports:
                - containerPort: 80
                - containerPort: 32111
        ```
        
        ```yaml
        kubectl create -f app-v1.yaml
        kubectl create -f app-v2-canary.yaml
        kubectl apply -f app-v2.yaml
        ```
        
    4. Dê um curl e veja a mágica acontecer:
        
        ```yaml
        curl 172.18.0.3:32222
        ```
        
        ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%206.png)
        
    
    ---
    
    ### **Um pouco mais de Rollouts e Rollbacks**
    
    **Verficando status de deployment com Rollout**
    
    ```yaml
    kubectl rollout status deloyment <nomeDeployment>
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%207.png)
    
  ## **Services**
    
    ### Introdução
    
    O que é service?
    
    - É uma forma de pegar um deployment para que possa ser acessado fora do cluster
    - Uma Camada que possa redirecionar as requisições externas para dentro do deployment.
    
    **Rodar o Autocomplete do kuberntes**
    
    ```bash
    
    echo "source <(kubectl completion bash)" >> /root/.bashrc
    ```
    
    - Certifique-se de que o bash-completion esteja instalado no linux
        
        ```bash
        sudo apt-get install -y bash-completion
        ```
        
    - Agora é só utilizar o seguinte comando para que o auto-complete do kubectl funcione
        
        ```bash
        kubectl completion bash > /etc/bash_completion.d/kubectl
        ```
        
    - Agora é so atualizar
        
        ```bash
        source <(kubectl completion bash)
        ```
        
    
    **OBS: Revisando:**
    
    > **Controllers:** Deployment → Replicaset → Pod
    > 
    > 
    > **Namespace:** É como se estivesse seprando por grade o cluster
    > 
    > - kube-system → namespace do kubernetes, onde possui os componentes do kubernetes
    > - Default: Onde subimos nossas aplicações por padrão
    > 
    > **kube-api-server:** É o cerebro do kubernetes, é o responsável por efetuar comunicações entre os nós
    > 
    > **kube-scheduller:** É o responsável por determinar em qual nó será hospedado
    > 
    > **kube-controller-manager:** Responsável pelo controle principal
    > 
    > **kube-proxy:** Responsável por  gerenciar a rede dos containers
    > 
    > **CNI:** Responsável por fazer as rede dos PODs funcionarem 
    > 
    
    ---
    
    ### Services
    
    Expor um  Serviço de um determinado POD
    
    ```yaml
    kubectl expose pod nginx --type=ClusterIp
    kubectl expose pod nginx --type=NodePort
    kubectl expose pod nginx --type=LoadBalancer
    ```
    
    **Vizualizar os Services**
    
    ```bash
    kubectl get services
    
    kubectl get svc
    ```
    
    **Vizualizar endpoints**
    
    ```bash
    kubectl get endpoints
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%208.png)
    
    ```bash
    kubectl scale --replicas=10 deployment nginx
    ```
    
    **Deletando o serviço**
    
    ```bash
    kubectl delete service <nome_service>
    ```
    
    **Tipo de Serviço: ClusterIp**
    
    - Comunicação interna entre Pods no cluster
    
    **yaml do service**
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      labels:
        run: nginx
      name: meu-nginx
      namespace: default
    spec:
      # Especifica o tipo de clusterIp
      #clusterIP: 10.96.114.95
      ports:
      # Porta do serviço
      - port: 80
        protocol: TCP
        # Porta do pod
        targetPort: 80
      # Vai definir para quem esse serviço ta funcionando
      # Para qual deployment esse serviço esta rodando? Para todo deployment que tiver a label app: nginx
      selector:
        app: nginx
      sessionAffinity: None
      # Tipo de serviço
      type: ClusterIP
    ```
    
    **Criando um service** 
    
    ```yaml
    kubectl create -f <arquivo.yaml>
    ```
    
    O que é esse **sessionAffinity:**
    
    - Cria um ClientIP para o serviço
    - Vai fazer afinidade pelo endereço do cliente
    - Vai pegar o IP de origem e vai usar o mesmo
    
    **Tipo de Serviço: NodePort**
    
    - Um tipo de serviço em NodePort, é um serviço que dá para se comunicar com a aplicação externamente do cluster.
    - Quando o NodePort é criado, ele cria tanto um ClusterIp quanto um NodePort
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        run: nginx
      namespace: default
    spec:
      ports:
        # Especificar uma determinada porta
        # range entre 30000-32767
        - nodePort: 32222
          port: 80
          protocol: TCP
          targetPort: 80
      selector:
        run: nginx
      sessionAffinity: None
      # Tipo de serviço
      type: NodePort
    ```
    
    **Tipo de Serviço: LoadBalancer**
    
    - Toda vez que criar um LoadBalancer, ele cria: ClusterIp, NodePort e um LoadBalancer
    
    **Expondo o tipo de serviço no deployment:**
    
    ```yaml
    kubectl expose deployment nginx --type=LoadBalancer
    
    kubectl get svc
    ```
    
    Após criar o serviço do deployment, perceba que  o EXTERNAL-IP está em pending: 
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%209.png)
    
    O status "<pending>" para o endereço IP externo (External IP) de um serviço LoadBalancer no Kubernetes geralmente indica que o serviço ainda está em processo de provisionamento e aguardando a atribuição de um endereço IP externo.
    
    Existem vários motivos para que esse tipo status ocorra:
    
    > **Provisionamento do LoadBalancer:** O provisionamento de 
    um serviço LoadBalancer envolve a interação com o provedor de nuvem 
    subjacente (por exemplo, AWS, GCP, Azure) para alocar um endereço IP 
    externo e configurar as regras do balanceador de carga.
    
    **Problemas de configuração:** Erros de configuração no 
    recurso de serviço, no controlador de ingresso ou nas permissões do 
    provedor de nuvem podem levar ao atraso na obtenção do endereço IP 
    externo.
    
    **Cota de recursos esgotada:** Em alguns provedores de nuvem, a atribuição de endereços IP externos pode estar sujeita a limites de cota. Se você atingiu a cota máxima de endereços IP externos, pode não ser possível provisionar um novo endereço IP até que a cota seja aumentada.
    > 
    > 
    > **Problemas no provedor de nuvem**: Às vezes, a infraestrutura do provedor de nuvem pode estar enfrentando problemas temporários que afetam a atribuição de endereços IP externos.
    > 
    > **Falta de recursos disponíveis:** Se o provedor de nuvem não tiver recursos de IP externos disponíveis no momento, você pode ver o status "<pending>" até que recursos adicionais estejam disponíveis.
    > 
    > **Cota de recursos esgotada:** Em alguns provedores de nuvem, a atribuição de endereços IP externos pode estar sujeita a limites de cota. Se você atingiu a cota máxima de endereços IP externos, pode não ser possível provisionar um novo endereço IP até que a cota seja aumentada.
    > 
    > **Problemas no provedor de nuvem:** Às vezes, a infraestrutura do provedor de nuvem pode estar enfrentando problemas temporários que afetam a atribuição de endereços IP externos.
    > 
    > **Falta de recursos disponíveis:** Se o provedor de nuvem não tiver recursos de IP externos disponíveis no momento, você pode ver o status "<pending>" até que recursos adicionais estejam disponíveis.
    > 
    
    Extraindo o yaml do Service com comando:
    
    ```bash
    kubectl get services nginx -o yaml > meu_primeiro_service_loadbalancer.yaml
    
    # Usando o neat para retirar linhas desnecessárias
    
    kubectl get services nginx -o yaml | kubectl neat > meu_primeiro_service_loadbalancer.yaml
    
    ```
    
    Quando um service é criado, automaticamente um endpoint e gerado
    
    ```bash
    kubectl get endpoints
    
    kubectl describe endpoints
    ```
    
    Aplicando mais de uma configuração yaml dentro de um arquivo através ‘—-’:
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      #annotations:
      name: meu-nginx
      labels:
        run: nginx
      namespace: default
    
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx 
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            imagePullPolicy: Always
            ports:
            - containerPort: 80
    
    ---
    
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        run: nginx
      namespace: default
    spec:
      ports:
        # Especificar uma determinada porta
        # range entre 30000-32767
        - nodePort: 32222
          port: 80
          protocol: TCP
          targetPort: 80
      selector:
        run: nginx
      sessionAffinity: None
      # Tipo de serviço
      type: NodePort
    ```
    
    ---
    
    ### Limitando Recursos
    
    ```yaml
    resources: 
        limits:
    			# Vai garantir no máximo 512Mi
    		   memory: 512Mi
          cpu: "500m"
        requests:
    			# Vai garantir em torno de 256Mi inicial
          memory: 256Mi
          cpu: "250m"
    ```
    
    **limits** → É o total que o kubernetes vai liberar de recursos para  o pod
    
    **requests** → É o quanto é que ele vai garantir de inicio, ou seja, ele é responsável por garantir uma determinada quantidade de recursos inicial
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      #annotations:
      name: meu-nginx
      labels:
        run: nginx
      namespace: default
    
    spec:
      replicas: 10
      selector:
        matchLabels:
          app: nginx 
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            imagePullPolicy: Always
            ports:
            - containerPort: 80
              protocol: TCP
            resources: 
              limits:
                memory: 512Mi
                cpu: "500m"
              requests:
                memory: 256Mi
                cpu: "250m"
    
          dnsPolicy: ClusterFirst
          restartPolicy: Always
          terminationGracePeriodSeconds: 30
    ```
    
    ```yaml
    kubectl replace -f deployment.yaml
    ```
    
    - Este é o comando que indica que você deseja substituir um recurso existente por um novo recurso definido no arquivo YAML.
    
    **Comando para editar um Service**
    
    ```yaml
    kubectl edit service nginx
    ```
    
    **Executando algo dentro de um container**
    
    ```bash
    kubectl exec -ti meu-pod -- bash
    
    # Dentro do container
    
    apt-get update && apt-get install -y stress
    
    # Teste de stress
    stress --vm 1 --vm-bytes 500M
    ```
    
    ---
    
    ### **LimitRange**
    
    **Crie um namespace**
    
    ```bash
    kubectl create namespace <nome>
    
    # Ver mais detalhes de um namespace
    kubectl describe namespace <nome>
    
    # Extraindo yaml do namespace
    kubectl get namespace <nome> -o yaml | kubectl neat > arquivo.yaml
    ```
    
    ---
    
    **Criar um limitRange**
    
    - É um objeto usado para limitar a quantidade recurso de determinado namespace
    
    ```yaml
    apiVersion: v1
    kind: LimitRange
    metadata:
      name: limitando-recurso
    spec:
      limits:
      - default:
          cpu: 1
          memory: 256Mi
        defaultRequest:
          cpu: 0.5
          memory: 128Mi
        type: Container
    ```
    
    ---
    
    **Aplicando o limitRange em um determinado namespace**
    
    ```yaml
    kubectl create -f namespace_limitado.yaml -n <namespace>
    
    kubectl get limitranges -n <namespace>
    ```
    
    ![Screenshot from 2023-11-01 22-12-36.png](Kubernetes%20730e37146baa485d9f46957707434f50/Screenshot_from_2023-11-01_22-12-36.png)
    
    ```yaml
    kubectl describe limitranges -n <namespace> <limitrange>
    ```
    
    ---
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2010.png)
    
    **Pod Limitado**
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: pod-limitado
      # Especificar o namespace onde o limitRange foi criado
      namespace: strigus
    spec:
      containers:
        - name: nginx
          image: nginx
    ```
    
    ```yaml
    kubectl create -f pod-limitado.yaml -n <namespace onde foi criado o limitRange>
    
    kubectl describe pods -n <namespace>
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2011.png)
    
    Basta apenas especificar o namespace onde foi criado o limitRanje, que a mágica acontece. 🤓
    
    ---
    
    ### **Taints**
    
    No Kubernetes, os "Taints" são usados para restringir quais nós (Nodes) um pod pode ser agendado. Os Taints são rótulos que são aplicados aos nós, especificando condições sob as quais um pod pode ser agendado neles. Isso é útil para separar recursos em clusters Kubernetes, definindo políticas de agendamento e garantindo que determinados pods sejam implantados apenas em nós específicos.
    
    Aqui está como os Taints funcionam:
    
    1. **Taints em um Nó:** Um administrador de cluster Kubernetes pode aplicar Taints a um nó. Cada Taint consiste em uma chave (key), um valor (value) e um efeito (effect).
    2. **Efeito do Taint:**
        - `NoSchedule`: Isso significa que nenhum pod será agendado no nó, a menos que o pod tenha um tolerância correspondente (Toleration) para esse Taint específico. Portanto, que esse cara não é para schedular mais nenhum container, ou seja, só vai manter os containers que estão em execução dele e não irá permitir mais nenhum outro container.
        - `PreferNoSchedule`: Isso indica que o Kubernetes tentará evitar agendar pods no nó, mas pode fazê-lo se necessário.
        - `NoExecute`: Isso significa que os pods já em execução no nó que não possuem uma tolerância correspondente serão evitados (ou expulsos).
    3. **Tolerações (Tolerations):** Os pods podem especificar tolerâncias que correspondem aos Taints aplicados aos nós. Isso permite que um pod seja agendado em nós com Taints específicos.
    
    A combinação de Taints e Tolerations permite que os administradores de clusters controlem a distribuição de pods em seu cluster, garantindo que pods críticos ou sensíveis a recursos sejam agendados apenas em nós apropriados. Por exemplo, você pode usar Taints para indicar nós com GPUs ou hardware específico e garantir que apenas os pods que podem aproveitar esses recursos sejam agendados nesses nós.
    
    Aqui está um exemplo de aplicação de um Taint em um nó e uma Tolerância correspondente em um pod:
    
    1. Aplicação de um Taint a um nó:
        
        ```
        kubectl taint nodes <node-name> key=value:effect
        ```
        
    2. Especificação de Tolerância em um pod manifest:
        
        ```yaml
        tolerations:
        - key: "key"
          operator: "Equal"
          value: "value"
          effect: "NoSchedule"
        ```
        
    
    Dessa forma, o pod com a tolerância correspondente poderá ser agendado no nó com o Taint específico.
    
    Os Taints são úteis para casos em que você precisa garantir que certos nós sejam reservados para cargas de trabalho específicas ou para isolar recursos em um cluster Kubernetes.
    
    ```yaml
    kubectl describe nodes <nome_do_node>
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2012.png)
    
    ---
    
    **Fazer com o que node de um determinado node não possa schedular**
    
    ```yaml
    kubecl taint node <nome_do_node> key1=value1:NoSchedule
    
    kubectl describe node <node>
    ```
    
    ---
    
    **Removendo a chave valor do taint**
    
    ```yaml
    kubectl taint node <nome_do_node> key1:NoSchedule-
    ```
    
    ---
    
    **Aplicando um taint NoExecute**
    
    ```yaml
    kubectl taint node <nome_do_node> key1=value1:NoExecute
    ```
    
    Assim que executado, os pods que estavam rodando nesse node migram para outros nodes automaticamente.
    
    ***OBS!*** Nesse caso é como se estivesse fazendo uma manutenção
    
    ---
    
    **Aplicando o taint em todos os nodes**
    
    ```bash
    kubectl taint nodes --all  key1=value1:NoExecute
    ```
    
    Após aplicado, nenhum pod pode ser aplicando nesses nodes, ou seja, todo cuidado é necessário
    
  ## **Volumes**
    
    ### EmptyDir
    
    **EmptyDir** → é um volume que vai ser atribuido um pod. fica grudado no pod e sempre vai iniciar vazio, ou seja, ele é criado junto do pod
    
    - Todos os containers dentro desse POD, pode fazer qualquer coisa
    - Outro ponto muito importante, é que não existe persistência de dados, caso o POD é removido você perde seus dados
    
    **Exemplo de um emptyDir em um POD**
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: busybox
      namespace: default
    spec:
      containers:
        - name: busybox
          image: busybox
          command: 
            - sleep
            - "3600"
          # Especificando um volume para o container
          volumeMounts:
            # Aonde o volume será montado dentro do container
            - mountPath: /giropops
            # Nome do volume
              name: giropops-dir
      #Definindo um volume vazio
      volumes:
          # Nome do volume vazio
        - name: giropops-dir
          # Tipo do volume vazio
          emptyDir: {}
    ```
    
    ```yaml
    kubectl describe pod busybox
    
    kubectl exec -ti busybox -- sh
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2013.png)
    
    **Criando um arquivo na pasta que foi criada na configuração do POD**
    
    ```yaml
    cd giropops
    touch teste.txt
    ```
    
    **Navegue pelo NÓ de trabalho onde o POD se encontra rodando e localize o arquivo que foi criado dentro do PO**
    
    **OBS!** O exemplo abaixo será feito em um NÓ criado pelo KIND
    
    ```bash
    docker exec -it <nome do no> sh
    
    # Vá em
    cd /var/lib/kubelet/pods
    
    ls
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2014.png)
    
    **Vai aparecer esse valores estranhos, use o comando find para localizar o diretório**
    
    ```bash
    find . -iname "giropops-dir"
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2015.png)
    
    ---
    
    ### Persistent Volumes (PV)
    
    **PV** → Persistent Volume
    
    **PVC** → Persistent Volume Clain
    
    - Cria um disco dentro cluster, determinado POD, Deployment…
    - É preciso que esse pesistent seja conectado há algum POD
        - Para isso necessário que tenha um Persistent Volume Clain (PVC)
    
    **Configurando o NFS em uma máquina onde se encontra o nó de controle**
    
    ```bash
    
    #Dentro do NÓ
    apt-get update
    
    # Instale isso no NÓ de controle
    apt-get install nfs-kernel-server
    # Instale isso no NÓ de trabalho
    apt-get install  nfs-common
    # Caso algum NÓ não venha instalado pór padrão
    apt-get install vim
    
    # Isso no nó de controle
    mkdir /opt/dados
    # Evitar fadiga
    chmod 1777 /opt/dados
    vim /etc/exports
    exportfs -ar
    ```
    
    Dentro do arquivo **exports**
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2016.png)
    
    **Explicando com mais detalhes:**
    
    O arquivo `/etc/exports` é uma configuração para o NFS (Network File System) no Linux. Aqui está o que cada parte significa:
    
    - `/opt/dados`: Este é o diretório que está sendo compartilhado. Os clientes NFS poderão acessar os arquivos neste diretório.
    - ``: Este é o especificador de host. Neste caso, o asterisco significa que qualquer host pode acessar este compartilhamento.
    - `(rw,sync,no_root_squash,subtree_check)`: Estas são as opções de exportação para este compartilhamento.
        - `rw`: Significa que o compartilhamento é de leitura e gravação. Os clientes podem ler e escrever arquivos no compartilhamento.
        - `sync`: Significa que as alterações no compartilhamento são escritas imediatamente ao disco, em vez de serem armazenadas em cache primeiro.
        - `no_root_squash`: Por padrão, o NFS muda o usuário root nos clientes para um usuário não privilegiado nos servidores, por razões de segurança. A opção `no_root_squash` desativa esse comportamento.
        - `subtree_check`: Esta opção habilita a verificação de subárvore, que ajuda a prevenir problemas quando partes de um sistema de arquivos são exportadas. No entanto, pode causar problemas de desempenho em alguns casos, então às vezes é desativada com `no_subtree_check`.
        
    
    Portanto, esta linha está configurando o diretório `/opt/dados` para ser compartilhado via NFS com qualquer host, permitindo leitura e gravação, escrevendo alterações imediatamente ao disco, permitindo ao usuário root nos clientes ter privilégios de root no servidor, e habilitando a verificação de subárvore.
    
    **O que é NFS e para que serve?**
    
    NFS, ou Network File System, é um protocolo de sistema de arquivos distribuídos que permite que os usuários em uma rede de computadores tenham acesso a arquivos e pastas em um servidor de rede. Ele foi desenvolvido pela Sun Microsystems.
    
    Com o NFS, os arquivos e diretórios localizados em um servidor de rede podem ser acessados de maneira semelhante aos arquivos e diretórios locais. Isso é feito montando o sistema de arquivos remoto no sistema local.
    
    O NFS é útil em ambientes onde os arquivos precisam ser compartilhados entre vários usuários ou sistemas. Por exemplo, em um ambiente de desenvolvimento de software, o código-fonte pode ser armazenado em um servidor NFS e acessado por vários desenvolvedores. Ou em um ambiente de escritório, os documentos podem ser armazenados em um servidor NFS e acessados por vários funcionários.
    
    No entanto, é importante notar que o NFS não criptografa o tráfego de rede, o que pode ser uma preocupação de segurança. Para ambientes onde a segurança é uma preocupação, outras soluções, como o SSHFS (que usa o protocolo seguro SSH), podem ser mais apropriadas.
    
    **YAML do Pesistent Volume (PV)**
    
    Para quem vai utilizar o NFS
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: primeiro-pv
    spec:
      # Tamanho do volume
      capacity:
        storage: 1Gi
      # Acesso ao volume (ReadWriteOnce, ReadOnlyMany, ReadWriteMany)  
      accessModes:
        - ReadWriteMany
      # Política de retenção do volume (Retain, Recycle, Delete) 
      # serve para definir o que acontece com o volume quando o pod é deletado 
      persistentVolumeReclaimPolicy: Retain
      # Definindo o tipo do volume (nfs, hostPath, glusterfs, cinder, cephfs, flocker, iscsi, rbd, flexVolume, vsphereVolume, quobyte, azureFile, photonPersistentDisk, portworxVolume, scaleIO, local, storageos)
      nfs:
        path: /opt/giropops
        server: 172.18.0.4
        readOnly: false
    ```
    
    Para quem vai utilizar um hostPath
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: hostpath-pv
    spec:
      capacity:
        storage: 1Gi
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      hostPath:
        path: "/opt/app"
    ```
    
    - Crie o seu NÓ o caminho /opt/app
    - Nesete exemplo estou utilizando o kind (Kubernetes in Docker)
    
    ```bash
    docker container exec -it kind-control-plane bash
    
    # Dentro do nó crie essa pasta
    mkdir /opt/app
    
    # Saita do nó
    exit
    ```
    
    O campo `accessModes` em um PersistentVolume (PV) no Kubernetes define como o volume pode ser acessado do cluster. Existem três modos de acesso:
    
    1. `ReadWriteOnce` (RWO): O volume pode ser montado como leitura-gravação por um único nó. Este é o modo mais comum e é suportado por praticamente todos os tipos de volumes.
    2. `ReadOnlyMany` (ROX): O volume pode ser montado como somente leitura por muitos nós. Isso é útil para compartilhar dados entre pods, mas não todos os tipos de volumes suportam este modo.
    3. `ReadWriteMany` (RWX): O volume pode ser montado como leitura-gravação por muitos nós. Isso é útil para compartilhar dados entre pods onde os dados também precisam ser alterados, mas nem todos os tipos de volumes suportam este modo.
    
    Nesse exemplo, o PersistentVolume está configurado com `ReadWriteMany`, o que significa que o volume pode ser montado como leitura-gravação por muitos nós. Isso é útil se você tiver vários pods que precisam ler e escrever no mesmo volume.
    
    Note que nem todos os tipos de volumes suportam todos os modos de acesso. Por exemplo, volumes do tipo `nfs` suportam todos os três modos, mas volumes do tipo `gcePersistentDisk` só suportam `ReadWriteOnce` e `ReadOnlyMany`.
    
    O campo `persistentVolumeReclaimPolicy` em um objeto PersistentVolume (PV) no Kubernetes determina o que acontece com um PV quando ele é liberado de sua reivindicação (PersistentVolumeClaim).
    
    Existem três políticas de recuperação possíveis]
    
    1. `Retain`: Esta é a política que você tem em seu exemplo. Quando um PV é liberado, ele não é automaticamente reciclado ou deletado. Em vez disso, ele permanece com seus dados intactos e passa para o estado `Released`. Isso permite a recuperação manual dos dados armazenados no volume. Para reutilizar o volume, é necessário apagar manualmente o PV e os dados associados.
    2. `Delete`: Quando um PV é liberado, o Kubernetes irá automaticamente deletar o PV e o armazenamento subjacente será também deletado.
    3. `Recycle`: Esta política está obsoleta a partir do Kubernetes 1.7. Quando um PV é liberado, os dados no volume são apagados e o volume pode ser reutilizado para uma nova reivindicação. No entanto, esta política não é segura e foi substituída pela funcionalidade de provisionamento dinâmico.
    
    Em resumo, a política `Retain` permite que você mantenha seus dados mesmo depois que o PV é liberado, mas requer limpeza manual antes que o volume possa ser reutilizado.
    
    **Comando para lista os PVs:**
    
    ```bash
    kubectl get pv
    ```
    
    **Descrever um pv**
    
    ```bash
    kubectl describe pv primeiro-pv
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2017.png)
    
    **YAML de um Persistent Volume Claim (PVC)**
    
    Vai servir para o NFS
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: primeiro-pvc
    spec:
      accessModes:
        - ReadWriteMany
      resources:
        # Tamanho do volume que será requisitado pelo pod
        requests:
          storage: 800Mi
    ```
    
    Vai servir para o hostpath
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: hostpath-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 1Gi
      storageClassName: ""
    ```
    
    **Comando para listar os PVCs:**
    
    ```yaml
    kubectl get pvc
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2018.png)
    
    **Montando o volume em um deployment**
    
    ```yaml
    apiVersion: app/v1
    kind: Deployment
    metadata:
      name: hostpath-dp
      labels:
        run: nginx
    spec:
      # tempo de espera para o pod ser considerado morto
      progressDeadlineSeconds: 600
      replicas: 1 
      # Quantidade de revisões que serão mantidas
      revisionHistoryLimit: 10
      selector:
        matchLabels:
          run: nginx
      # Estratégia de atualização do deployment
      strategy:
        rollingUpdate:
          maxSurge: 1
          maxUnavailable: 1
        type: RollingUpdate
      template:
        metadata:
          labels:
            run: nginx
        spec:
          containers:
          - image: nginx
            imagePullPolicy: Always
            name: nginx
            # volume que será montado no container
            volumeMounts:
              - name: hostpath-pv
                mountPath: /opt/app
            resources: {}
            terminationMessagePath: /dev/termination-log
            terminationMessagePolicy: File
          # Especificando o volume que será montado no container
          volumes:
            - name: hostpath-pv
              persistentVolumeClaim:
                claimName: hostpath-pvc
          # DNS Policy do pod
          dnsPolicy: ClusterFirst
          # Reiniciar o pod sempre que ele morrer
          restartPolicy: Always
          # scheduler que será utilizado para agendar o pod
          schedulerName: default-scheduler
          # Segurança do pod
          securityContext: {}
          # maneira de como o pod será finalizado
          terminationGracePeriodSeconds: 30
    ```
    
    ```yaml
    kubectl describe deployment hostpath-dp
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2019.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2020.png)
    
    ---
    
    - **OBS Importante**
        
        Se você esté tendo esse problema ao dá um get no pvc
        
        ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2021.png)
        
        Existem 2 motivos:
        
        1. é porque o caminho em especifico está errado e por isso ele está em pendind
        2. Já o outro é por causa de que o NÓ de controle não esta configura com o nfs-kernel-server.
        - No meu NÓ em especifico estava tentando dá um exportfs -ar e me mostrava esse determinado erro **exportfs: /opt/dados does not suport NFS export**, daí eu rodei o seguinte comando:
        
        ```bash
        service nfs-kernel-server status
        
        # ou
        
        systemctl status nfs-kernel-server
        ```
        
        E me veio este erro:
        
        ```bash
        ● nfs-server.service - NFS server and services
             Loaded: loaded (/lib/systemd/system/nfs-server.service; enabled; vendor preset: enabled)
             Active: inactive (dead)
        ```
        
        E em seguida rodei o seguinte comando para dá um start:
        
        ```bash
        service nfs-kernel-server start
        ```
        
        e me veio outros seguintes Erros
        
        ```bash
        Job for nfs-server.service canceled
        
        root@kind-efk-control-plane:~# service nfs-kernel-server status
        ● nfs-server.service - NFS server and services
             Loaded: loaded (/lib/systemd/system/nfs-server.service; enabled; vendor preset: enabled)
             Active: failed (Result: exit-code) since Wed 2023-11-15 23:31:12 UTC; 5s ago
            Process: 15395 ExecStartPre=/usr/sbin/exportfs -r (code=exited, status=1/FAILURE)
            Process: 15396 ExecStopPost=/usr/sbin/exportfs -au (code=exited, status=0/SUCCESS)
            Process: 15397 ExecStopPost=/usr/sbin/exportfs -f (code=exited, status=0/SUCCESS)
                CPU: 6ms
        
        Nov 15 23:31:12 kind-efk-control-plane systemd[1]: Starting NFS server and services...
        Nov 15 23:31:12 kind-efk-control-plane exportfs[15395]: exportfs: /opt/dados does not support NFS export
        Nov 15 23:31:12 kind-efk-control-plane systemd[1]: nfs-server.service: Control process exited, code=exited, status=1/FAILURE
        Nov 15 23:31:12 kind-efk-control-plane systemd[1]: nfs-server.service: Failed with result 'exit-code'.
        Nov 15 23:31:12 kind-efk-control-plane systemd[1]: Stopped NFS server and services.
        ```
        
        Solução:
        
        - Antes de fazer todos esses procedimentos, eu tinha instalado nesse nó o **nfs-common**, e logo após instalei o **nfs-kernel-server**, daí gerou um conflito entre esses dois, e para que o nfs-kernel-server pudesse rodar a solução foi remover o **nfs-common** com o seguinte comando:
            
            ```bash
            apt-get remove nfs-common
            ```
            
        - E em seguida verifiquei o status do nfs-server
            
            ```bash
            root@kind-efk-control-plane:~# systemctl status nfs-kernel-server
            ● nfs-kernel-server.service - LSB: Kernel NFS server support
                 Loaded: loaded (/etc/init.d/nfs-kernel-server; generated)
                 Active: active (exited) since Wed 2023-11-15 23:50:50 UTC; 8s ago
                   Docs: man:systemd-sysv-generator(8)
                Process: 16907 ExecStart=/etc/init.d/nfs-kernel-server start (code=exited, status=0/SUCCESS)
                    CPU: 2ms
            
            Nov 15 23:50:50 kind-efk-control-plane systemd[1]: Starting LSB: Kernel NFS server support...
            Nov 15 23:50:50 kind-efk-control-plane systemd[1]: Started LSB: Kernel NFS server support.
            ```
            
            E estava ativo
            
            Problema solucionado com sucesso…
            
            Fonte da solução:
            
            [https://chat.openai.com/share/b50493c0-2576-4d24-ad97-6d492fa5b811](https://chat.openai.com/share/b50493c0-2576-4d24-ad97-6d492fa5b811)
            
        
  ## **Cronjobs**
    
    
    CronJobs é um recurso do Kubernetes que permite executar tarefas programadas (jobs) em intervalos específicos. É semelhante à funcionalidade do cron no Unix/Linux, que executa scripts em horários programados.
    
    Um CronJob cria Jobs em um cronograma baseado em tempo. O cronograma é definido usando a sintaxe cron padrão.
    
    Aqui está um exemplo de um CronJob que executa um Job a cada minuto:
    
    ```yaml
    apiVersion: batch/v1beta1
    kind: CronJob
    metadata:
      name: hello
    spec:
      schedule: "*/1 * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: hello
                image: busybox
                args:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
              restartPolicy: OnFailure
    
    ```
    
    Neste exemplo, o CronJob `hello` está programado para rodar a cada minuto. O Job que ele cria executa um único pod que imprime a data e hora atual e uma mensagem.
    
    Lembre-se de que os CronJobs estão em beta no Kubernetes e podem não estar disponíveis em todos os clusters.
    
    outro exemplo:
    
    ```yaml
    apiVersion: batch/v1
    kind: CronJob
    metadata:
      name: giropops-cron
    spec:
      # formato de como escrever um crontabe segue essa sequencia:
      # (minuto, hora, dia, mês, dia da semana comando) 
      schedule: "*/1 * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: giropops-cron
                image: busybox
                # comando que será executado no container
                args:
                - /bin/sh
                - -c
                - date; echo Sejba bem vindo ao curso de Kubernetes; sleep 30
              restartPolicy: OnFailure
    
    # minutos hora dia mes dia-da-semana comando
    # */1 -> qualquer minuto
    # * -> qualquer dia
    # * -> qualquer mês
    # * -> qualquer dia da semana
    # * ->  qualquer comando
    # 10 08 * * 1-5 comando -> executar o comando de segunda a sexta as 08:10
    # 0-59 0-23 1-31 1-12 0-7 
    # */1 1,2 1-10 -> vai executar a cada minuto, nas horas 1 e 2, nos dias 1 a 10
    ```
    
    - `apiVersion: batch/v1`: Define a versão da API do Kubernetes que este objeto pertence. `batch/v1` é a versão que inclui o tipo de objeto `CronJob`.
    - `kind: CronJob`: Define o tipo de objeto Kubernetes que está sendo criado. Neste caso, é um `CronJob`.
    - `metadata:`: Contém metadados sobre o objeto, como seu nome e namespace.
        - `name: giropops-cron`: Define o nome do CronJob.
    - `spec:`: Define as especificações do CronJob.
        - `schedule: "*/1 * * * *"`: Define o cronograma para o CronJob em formato cron. Neste caso, o job será executado a cada minuto.
        - `jobTemplate:`: Define o template para os Jobs que este CronJob irá criar.
            - `spec:`: Define as especificações para os Jobs.
                - `template:`: Define o template para os Pods que o Job irá criar.
                    - `spec:`: Define as especificações para os Pods.
                        - `containers:`: Define a lista de contêineres que o Pod irá executar.
                            - `name: giropops-cron`: Define o nome do contêiner.
                            - `image: busybox`: Define a imagem Docker que o contêiner irá usar.
                            - `args:`: Define os argumentos que serão passados para o contêiner no momento da inicialização.
                                - `/bin/sh`
                                - `-c`
                                - `date; echo Sejba bem vindo ao curso de Kubernetes; sleep 30`: Estes comandos serão executados no contêiner. Primeiro, ele imprimirá a data e hora atual, depois imprimirá a mensagem "Sejba bem vindo ao curso de Kubernetes", e então dormirá por 30 segundos.
                        - `restartPolicy: OnFailure`: Define a política de reinicialização para os contêineres no Pod. Neste caso, se um contêiner falhar, ele será reiniciado.
    
    ```yaml
    kubectl get cronjobs
    
    kubectl get jobs
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2022.png)
    
    Verfique as infromações desse cronjob e depois verifique os logs
    
    ```bash
    kubectl describe cronjobs giropops-cron
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2023.png)
    
    ```bash
    kubectl logs giropops-cron-28343206-npxm2
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2024.png)
    
  ## **Secrets**
    
    
    Secrets no Kubernetes são um objeto que contém um pequeno volume de dados sensíveis, como uma senha, um token ou uma chave. Essas informações podem ser usadas por pods para realizar ações.
    
    Os Secrets são úteis para armazenar informações que você não quer que sejam expostas em seu aplicativo, como credenciais de acesso a um banco de dados. Eles são armazenados no cluster Kubernetes e podem ser montados em um pod como um volume ou expostos como variáveis de ambiente.
    
    - **secret**  - Sempre quando colocar uma chave valor, e esses chave valor não ficar disponível diretamente no código.
    
    Aqui está um exemplo de como criar um Secret com um par de nome de usuário e senha:
    
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: mysecret
    type: Opaque
    data:
      username: YWRtaW4=  # Base64 encoded 'admin'
      password: MWYyZDFlMmU2N2Rm  # Base64 encoded '1f2d1e2e67df'
    
    ```
    
    Neste exemplo, o Secret chamado `mysecret` contém dois dados: `username` e `password`. Os valores são codificados em Base64.
    
    Lembre-se de que os Secrets não são uma forma segura de armazenar informações sensíveis, pois o conteúdo dos Secrets pode ser obtido por qualquer usuário com acesso ao cluster. Eles são apenas uma maneira de evitar que essas informações sejam expostas em seu código. Para uma segurança mais robusta, você deve considerar soluções adicionais, como o uso de uma ferramenta de gerenciamento de segredos externa.
    
    **Criando um Secret baseado no arquivo secret.txt**
    
    ```bash
    echo -n "Diodai Dodinho Yodai" > secret.txt
    
    kubectl create secret generic my-secret --from-file=secret.txt
    ```
    
    Explicando a linha de comando pasada:
    
    Este comando cria um objeto Secret no Kubernetes usando o comando `kubectl create secret`. Aqui está o que cada parte do comando faz:
    
    - `kubectl`: Esta é a ferramenta de linha de comando que permite interagir com o cluster Kubernetes.
    - `create secret`: Este comando cria um novo objeto Secret.
    - `generic`: Este é o tipo de Secret que está sendo criado. Um Secret genérico é um Secret que pode conter qualquer dado, mas não tem um esquema específico.
    - `my-secret`: Este é o nome que você está dando ao Secret.
    - `-from-file=secret.txt`: Esta opção indica que o Secret deve ser preenchido com o conteúdo do arquivo `secret.txt`. Cada linha no arquivo será transformada em um item no Secret.
    
    Então, se o conteúdo do arquivo `secret.txt` é `Diodai Dodinho Yodai`, o Secret criado terá um item com a chave `secret.txt` e o valor `Diodai Dodinho Yodai`.
    
    Lembre-se de que os valores em um Secret são codificados em base64, então o valor real armazenado será a string `Diodai Dodinho Yodai` codificada em base64.
    
    ```bash
    kubectl describe secrets my-secret
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2025.png)
    
    ```bash
    # Pegar todos os secrets criados
    kubectl get secrets
    
    #Pegar o yaml de um determinado secret criado
    kubectl get secrets my-secret -oyaml
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2026.png)
    
    - São codificados em base64
    - Para decodificar, pegue o valor do token criado pelo secret, no exemplo acima pegue este **RGlvZGFpIERvZGluaG8gWW9kYWk=** execute o seguinte comando:
    
    ```bash
    echo '**RGlvZGFpIERvZGluaG8gWW9kYWk=**' | base64 --decode
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2027.png)
    
    **Criando Secret de forma literal**
    
    ```bash
    kubectl create secret generic my-literal-secret --from-literal user=wolf --from-literal password=tbwa0002
    ```
    
    **Describe**
    
    ```bash
    kubectl describe secret my-literal-secret
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2028.png)
    
    **Criando um pod e inserindo variáveis de ambiente com os valores do secret**
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: teste-secret-env
      namespace: default
    spec:
      containers:
        - image: busybox
          name: busy-secret-env
          command:
            - sleep
            - "3600"
          env:
          - name: MEU_USERNAME
            #Valor de algum lugar, ou seja, de uma secretkeyref
            valueFrom:
              # Valor da chave user do secret my-literal-secret que será usado na variável MEU_USERNAME
              secretKeyRef:
                name: my-literal-secret
                # Chave do secret my-literal-secret que será usado na variável MEU_USERNAME
                key: user
          - name: MEU_PASSWORD
            valueFrom:
              secretKeyRef:
                name: my-literal-secret
                key: password
    ```
    
    ```yaml
    kubectl get secret
    
    kubectl exec -it teste-secret-env -- sh
    
    # Dentro do pod digite o seguinte comando para vizualizar as variáveis de ambiente
    set
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2029.png)
    
  ## **Configmaps**
    
    
    É um recurso que permite armazenar configurações não confidenciais em formato de pares chave-valor. Essas configurações podem ser utilizadas pelos pods durante a execução. O objetivo principal do ConfigMap é separar a configuração do código-fonte da aplicação, permitindo que você altere a configuração sem modificar o código da aplicação.
    
    Aqui estão alguns pontos-chave sobre ConfigMaps no Kubernetes:
    
    1. **Formato de Dados:**
        - O ConfigMap armazena dados como pares chave-valor.
        - Esses dados podem incluir configurações de ambiente, configurações de inicialização ou qualquer outra informação não sensível.
    2. **Cenários de Uso:**
        - Configurações de ambiente para containers/pods.
        - Configurações para aplicativos.
        - Configurações para volumes.
    3. **Criação de ConfigMap:**
        - Você pode criar um ConfigMap diretamente usando arquivos de definição YAML ou através do comando `kubectl create configmap`.
        - Exemplo de criação usando YAML:
            
            ```yaml
            apiVersion: v1
            kind: ConfigMap
            metadata:
              name: meu-configmap
            data:
              CHAVE1: valor1
              CHAVE2: valor2
            ```
            
    4. **Referenciando ConfigMaps em Pods:**
    - Os dados do ConfigMap podem ser injetados nos pods como variáveis de ambiente ou volumes.
    - Exemplo de referência em um pod usando variáveis de ambiente:
        
        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          name: meu-pod
        spec:
          containers:
          - name: meu-container
            image: minha-imagem
            env:
            - name: CHAVE1
              valueFrom:
                configMapKeyRef:
                  name: meu-configmap
                  key: CHAVE1
            - name: CHAVE2
              valueFrom:
                configMapKeyRef:
                  name: meu-configmap
                  key: CHAVE2
        ```
        
    1. **Atualização Dinâmica:**
        - Alterações nos dados do ConfigMap podem ser refletidas automaticamente nos pods que referenciam esses dados.
        - Isso facilita a atualização dinâmica da configuração sem reiniciar os pods.
    2. **Volume Baseado em ConfigMap:**
        - Além de variáveis de ambiente, você pode montar um volume baseado em ConfigMap diretamente nos pods.
        - Isso permite que arquivos de configuração sejam acessados pelo caminho do sistema de arquivos do contêiner.
        
    
    Rodando um configmap via linha de comando:
    
    ```yaml
    kubectl create configmap cores-frutas --from-literal uva=roxa --from-file=predileta --from-file=frutas/
    
    kubectl get configmap
    
    kubectl describe configmap cores-frutas
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2030.png)
    
    YAML do configmap de cores-frutas
    
    ```yaml
    apiVersion: v1
    data:
      banana: |
        amarela
      limao: |
        verde
      melancia: |
        Verde e vermelho
      morango: |
        vermelho
      predileta: |
        kiwi
      uva: roxa
    kind: ConfigMap
    metadata:
      name: cores-frutas
      namespace: default
    ```
    
    - `apiVersion: v1`: Define a versão da API do Kubernetes que este objeto pertence.
    - `kind: ConfigMap`: Define o tipo de objeto Kubernetes que está sendo criado. Neste caso, é um `ConfigMap`.
    - `metadata:`: Contém metadados sobre o objeto, como seu nome e namespace.
        - `name: cores-frutas`: Define o nome do ConfigMap.
        - `namespace: default`: Define o namespace onde o ConfigMap será criado. Se não especificado, o namespace `default` é usado.
    - `data:`: Contém os dados que serão armazenados no ConfigMap. Cada chave sob `data` se torna o nome de um arquivo, e o valor da chave se torna o conteúdo do arquivo quando o ConfigMap é montado em um Pod.
        - `banana: | amarela`, `limao: | verde`, `melancia: | Verde e vermelho`, `morango: | vermelho`, `predileta: | kiwi`, `uva: roxa`: Estes são os pares chave-valor que serão armazenados no ConfigMap. A barra vertical `|` permite que você insira um valor de várias linhas. Neste caso, cada fruta tem uma cor associada.
    
    Referenciando um configmap em um POD:
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: busybox-configmap
      namespace: default
    spec:
      containers:
        - image: busybox
          name: busy-configmap
          command:
           - sleep
           - "3600"
          env:
          - name: frutas
            valueFrom:
              configMapKeyRef:
                name: cores-frutas
                key: predileta
    ```
    
    ```yaml
    kubectl exec -ti busybox-configmap -- sh
    
    # Dentro do pod
    
    set
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2031.png)
    
    ```yaml
    kubectl delete -f pod-configmap.yaml
    ```
    
    Agora suba um pod que referencia todas as chaves de cores de frutas
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: busybox-configmap
      namespace: default
    spec:
      containers:
        - image: busybox
          name: busy-configmap
          command:
           - sleep
           - "3600"
          envFrom:
          # traz todas as chaves do configmap cores-frutas
          - configMapRef:
              name: cores-frutas
    ```
    
    ```yaml
    kubectl exec -ti busybox-configmap -- sh
    
    # Dentro do pod
    
    set
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2032.png)
    
    Criando um pod com configmap dentro de um arquivo
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: busybox-configmap-file
      namespace: default
    spec:
      containers:
        - image: busybox
          name: busy-configmap
          command:
            - sleep
            - "3600"
          volumeMounts:
          - name: meu-configmap-vol
            mountPath: /etc/frutas
      volumes:
      - name: meu-configmap-vol
        configMap:
          name: cores-frutas
    ```
    
    ```yaml
    kubectl exec -ti busybox-configmap-file -- sh
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2033.png)
    
  ## **InitContainer**
    
    
    Executa uma tarefa antes do meu POD principal
    
    - Imagine uma aplicação, e para a aplicação rode, antes é preciso que rodar um comando para gerar dados, logo, vai exisitir um outro container que vai executar essa determinada tarefa antes da aplicação
    - Um initContainer → É um container que vai executar uma ação específica antes do container propriamente dito ser executado
    
    YAML de um pod initContainer
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: init-demo
      labels:
        app: init-demo
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - name: workdir
              mountPath: /usr/share/nginx/html
      initContainers:
        - name: install
          image: busybox
          # vai fazer o download do index.html do site kubernetes.io
          command:
          - wget
          - "--no-check-certificate"
          - "-O"
          - "/work-dir/index.html"
          - https://www.docker.com/
          volumeMounts:
            - name: workdir
              mountPath: "/work-dir"
      dnsPolicy: Default
      volumes:
        - name: workdir
          emptyDir: {}
    ```
    
    - `apiVersion: v1`: Define a versão da API do Kubernetes que este objeto pertence.
    - `kind: Pod`: Define o tipo de objeto Kubernetes que está sendo criado. Neste caso, é um `Pod`.
    - `metadata:`: Contém metadados sobre o objeto, como seu nome e labels.
        - `name: init-demo`: Define o nome do Pod.
        - `labels:`: Define os labels do Pod, que são usados para identificar o Pod entre outros objetos no Kubernetes.
    - `spec:`: Define as especificações do Pod.
        - `containers:`: Define a lista de contêineres que o Pod irá executar.
            - `name: nginx`: Define o nome do contêiner.
            - `image: nginx`: Define a imagem Docker que o contêiner irá usar.
            - `ports:`: Define as portas que o contêiner irá expor.
            - `volumeMounts:`: Define os volumes que serão montados no contêiner.
        - `initContainers:`: Define a lista de contêineres de inicialização que serão executados antes dos contêineres principais.
            - `name: install`: Define o nome do contêiner de inicialização.
            - `image: busybox`: Define a imagem Docker que o contêiner de inicialização irá usar.
            - `command:`: Define o comando que será executado quando o contêiner de inicialização iniciar. Neste caso, ele está fazendo o download da página inicial do site Docker e salvando-a como `index.html` no diretório de trabalho.
        - `dnsPolicy: Default`: Define a política de DNS para o Pod.
        - `volumes:`: Define os volumes que serão disponibilizados para os contêineres no Pod.
        
        Porquê ele baixou o o site pelo busybox e a mesma está funcianando no container do nginx?
        
        O contêiner `install` é um contêiner de inicialização (init container). Os contêineres de inicialização são executados antes do contêiner principal e são projetados para configurar o ambiente necessário para o contêiner principal. Eles devem concluir com sucesso antes que os contêineres principais comecem.
        
        Neste caso, o contêiner de inicialização `install` está usando a imagem `busybox` para executar o comando `wget` e baixar a página inicial do site Docker. Ele então salva essa página como `index.html` no volume `workdir`.
        
        O volume `workdir` é um volume do tipo `emptyDir`, o que significa que ele é inicialmente vazio e é criado quando o Pod é atribuído a um nó. Qualquer dado que é escrito no volume é preservado através de reinicializações do contêiner.
        
        O contêiner principal `nginx` também monta o volume `workdir` no caminho `/usr/share/nginx/html`, que é o diretório onde o nginx serve arquivos estáticos por padrão. Portanto, quando o contêiner `nginx` começa, ele já tem o arquivo `index.html` que foi baixado pelo contêiner de inicialização, e ele pode servir esse arquivo quando recebe uma solicitação HTTP.
        
        Então, para responder à sua pergunta, o contêiner `install` baixa o site usando `busybox` e `wget` porque `busybox` é uma imagem leve que inclui o utilitário `wget`, e o site baixado pode ser servido pelo contêiner `nginx` porque ambos os contêineres compartilham o volume `workdir`.
        
   ## RBAC
    
    
    RBAC (Role-Based Access Control) é um método de **controle de acesso** que é usado no Kubernetes para gerenciar permissões sobre quem pode fazer o quê dentro do cluster.
    
    No RBAC, você define políticas de acesso em termos de Roles e RoleBindings:
    
    - Uma `Role` define um conjunto de permissões, como a capacidade de criar pods ou listar serviços. Essas permissões são definidas em termos de recursos do Kubernetes (como pods, services, etc.) e operações (como get, list, create, delete, etc.).
    - Uma `RoleBinding` associa uma Role a um ou mais usuários, grupos ou ServiceAccounts. Isso significa que esses usuários, grupos ou ServiceAccounts têm as permissões definidas na Role.
    - 
    
    Há também `ClusterRoles` e `ClusterRoleBindings`, que são como Roles e RoleBindings, mas se aplicam a todo o cluster, não apenas a um namespace específico.
    
    Aqui está um exemplo de uma Role e uma RoleBinding no Kubernetes:
    
    ```yaml
    kind: Role
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      namespace: default
      name: pod-reader
    rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["get", "watch", "list"]
    ---
    kind: RoleBinding
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      name: read-pods
      namespace: default
    subjects:
    - kind: User
      name: jane
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: Role
      name: pod-reader
      apiGroup: rbac.authorization.k8s.io
    
    ```
    
    Neste exemplo, a Role `pod-reader` permite que o usuário leia pods no namespace `default`. A RoleBinding `read-pods` concede essa Role ao usuário `jane`. Portanto, `jane` pode ler pods no namespace `default`.
    
    - **ServiceAccount**: No Kubernetes, um ServiceAccount é um objeto que identifica processos que rodam em um Pod. Em outras palavras, enquanto usuários humanos usam contas de usuário para acessar recursos do Kubernetes, aplicações usam ServiceAccounts. Quando um Pod é criado, ele é automaticamente associado a um ServiceAccount. Se nenhum for especificado, o ServiceAccount `default` é usado. Tokens de ServiceAccount são armazenados como Secrets e montados em Pods.
    
    - **ClusterRole**: Um ClusterRole é um objeto que define um conjunto de permissões que podem ser aplicadas em todo o cluster, ou em um conjunto específico de namespaces, para um conjunto específico de recursos (por exemplo, Pods, Services, etc.). As permissões são definidas em termos de operações (como get, list, create, delete, etc.). Por exemplo, um ClusterRole pode permitir a leitura de todos os Pods em todos os namespaces.
    
    - **ClusterRoleBinding**: Um ClusterRoleBinding é um objeto que associa um ClusterRole a um conjunto de usuários, grupos ou ServiceAccounts. Isso significa que esses usuários, grupos ou ServiceAccounts têm as permissões definidas no ClusterRole. Por exemplo, um ClusterRoleBinding pode conceder a um ServiceAccount específico a permissão de ler todos os Pods em todos os namespaces.
    
    Aqui está um exemplo de como esses três conceitos podem ser usados juntos:
    
    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: my-service-account
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
      name: pod-reader
    rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["get", "watch", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: read-pods
    subjects:
    - kind: ServiceAccount
      name: my-service-account
      namespace: default
    roleRef:
      kind: ClusterRole
      name: pod-reader
      apiGroup: rbac.authorization.k8s.io
    
    ```
    
    Neste exemplo, um ServiceAccount chamado `my-service-account` é criado. Um ClusterRole chamado `pod-reader` é criado, que permite a leitura de Pods. Um ClusterRoleBinding chamado `read-pods` é criado, que associa o ServiceAccount `my-service-account` ao ClusterRole `pod-reader`. Portanto, qualquer Pod que use o ServiceAccount `my-service-account` pode ler Pods em todos os namespaces.
    
    Criando um service account via linda de comando:
    
    ```yaml
    kubectl create serviceaccount erick
    
    kubectl get serviceaccount
    ```
    
    Listando cluster roles
    
    ```yaml
    kubectl get clusterrole
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2034.png)
    
    Pegando informação de um determinado cluster role
    
    ```yaml
    kubectl describe clusterrole cluster-admin
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2035.png)
    
    `cluster-admin` no Kubernetes. Aqui está o que cada parte do resumo significa:
    
    - `Name: cluster-admin`: Este é o nome do ClusterRole. `cluster-admin` é um ClusterRole predefinido que dá permissões completas sobre todos os recursos.
    - `Labels: kubernetes.io/bootstrapping=rbac-defaults`: Labels são pares chave-valor que são anexados aos objetos para fins de organização e seleção. Neste caso, o label indica que este ClusterRole é parte do bootstrap padrão do RBAC do Kubernetes.
    - `Annotations: rbac.authorization.kubernetes.io/autoupdate: true`: As anotações são semelhantes aos labels, mas são usadas para anexar informações não identificáveis aos objetos. Neste caso, a anotação indica que este ClusterRole deve ser atualizado automaticamente pelo sistema.
    
    - `PolicyRule:`: Esta seção lista as regras de política que definem as permissões concedidas por este ClusterRole.
        - `Resources *.*`: Esta regra indica que o ClusterRole tem permissões sobre todos os recursos em todos os grupos de recursos.
        - `Non-Resource URLs [*]`: Esta regra indica que o ClusterRole tem permissões sobre todas as URLs não associadas a recursos.
        - `Resource Names []`: Como esta lista está vazia, a regra não se aplica a nenhum nome de recurso específico, mas a todos os recursos.
        - `Verbs [*]`: Esta regra indica que o ClusterRole tem permissões para realizar todas as ações (get, list, create, delete, etc.) em todos os recursos e URLs não associadas a recursos.
        
    
    Em resumo, o ClusterRole `cluster-admin` tem permissões completas para fazer qualquer coisa em qualquer recurso ou URL não associada a recursos no cluster.
    
    ```yaml
    kubectl describe clusterrole view
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2036.png)
    
    Listando cluster role biding
    
    ```yaml
    kubectl get clusterrolebindings
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2037.png)
    
    ```yaml
    kubectl describe clusterrolebindings cluster-admin
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2038.png)
    
    Criando um **clusterrolebindings** via linda de comando
    
    ```yaml
    kubectl create clusterrolebinding yodai --serviceaccount=default:erick --clusterrole=cluster-admin
    
    kubectl get clusterrolebinding
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2039.png)
    
    ```yaml
    kubectl describe clusterrolebinding yodai
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2040.png)
    
    ```yaml
    kubectl describe serviceaccount erick
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2041.png)
    
    YAML ServiceAccount
    
    ```yaml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: admin-user
      namespace: kube-system
    ```
    
    YAML ClusterRoleBinding
    
    ```yaml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
      name: admin-user
    roleRef:
      # vai dar permissão de administrador para o usuário admin-user
      apiGroup: rbac.authorization.k8s.io/v1
      # Associando o usuário admin-user ao cluster-admin
      kind: ClusterRole
      name: cluster-admin
    # é possível associar um usuário a um grupo de usuários ou a um serviço de conta
    # Associando o service account admin-user ao clustr-admin
    subjects:
    - kind: ServiceAccount
      name: admin-user
      namespace: kube-system
    ```
    
     Este é um objeto `ClusterRoleBinding` no Kubernetes. Ele está associando um `ServiceAccount` chamado `admin-user` no namespace `kube-system` ao `ClusterRole` chamado `cluster-admin`. Aqui está o que cada parte do arquivo faz:
    
    - `apiVersion: rbac.authorization.k8s.io/v1`: Define a versão da API do Kubernetes que este objeto pertence.
    - `kind: ClusterRoleBinding`: Define o tipo de objeto Kubernetes que está sendo criado. Neste caso, é um `ClusterRoleBinding`.
    - `metadata:`: Contém metadados sobre o objeto, como seu nome.
        - `name: admin-user`: Define o nome do ClusterRoleBinding.
    - `roleRef:`: Define a referência para o ClusterRole que está sendo associado.
        - `apiGroup: rbac.authorization.k8s.io/v1`: Define o grupo de API do ClusterRole.
        - `kind: ClusterRole`: Define o tipo de objeto ao qual a referência se aplica.
        - `name: cluster-admin`: Define o nome do ClusterRole ao qual a referência se aplica.
    - `subjects:`: Define a lista de sujeitos que serão associados ao ClusterRole.
        - `kind: ServiceAccount`: Define o tipo de sujeito que está sendo associado.
        - `name: admin-user`: Define o nome do ServiceAccount que está sendo associado.
        - `namespace: kube-system`: Define o namespace do ServiceAccount que está sendo associado.
    
    Portanto, este ClusterRoleBinding está dando ao ServiceAccount `admin-user` no namespace `kube-system` as permissões definidas no ClusterRole `cluster-admin`, que geralmente inclui permissões completas para todos os recursos em todo o cluster.
    
    ```yaml
    kubectl create -f admin-cluster-role-binding.yaml
    kubectl get clusterrolebinding
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2042.png)
    
    ```yaml
    kubectl describe clusterrolebinding admin-user
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2043.png)
    
  ## **Helm**
    
    
    O Helm é uma ferramenta popular de código aberto para gerenciar aplicativos Kubernetes. Kubernetes é um sistema de orquestração de contêineres amplamente utilizado para automatizar a implantação, escalonamento e gerenciamento de contêineres em ambientes de produção.
    
    O Helm simplifica o processo de empacotamento, distribuição e gerenciamento de aplicativos Kubernetes. Ele permite que você defina, instale e atualize facilmente aplicativos complexos em um cluster Kubernetes. Aqui estão alguns dos conceitos-chave associados ao Helm:
    
    1. **Chart:** Um pacote Helm é chamado de "chart". Um chart é uma coleção de arquivos que descrevem um conjunto de recursos do Kubernetes. Ele inclui definições para implantações, serviços, ingressos, configurações e outros recursos necessários para executar um aplicativo em um cluster Kubernetes.
    2. **Release:** Uma instância específica de um chart em um cluster Kubernetes é chamada de "release". Cada release representa uma implantação específica de um aplicativo em execução.
    3. **Values:** Os charts Helm podem ser parametrizados usando arquivos de valores. Isso permite que você personalize a configuração do aplicativo, como a quantidade de réplicas, portas, senhas, etc., sem modificar o próprio chart.
    4. **Repositórios:** Os charts Helm podem ser armazenados em repositórios para facilitar o compartilhamento e a distribuição. Helm permite que você pesquise e instale charts diretamente de repositórios remotos.
    5. **Helm CLI:** Helm é usado principalmente por meio de uma interface de linha de comando (CLI). Com a CLI do Helm, você pode instalar, atualizar, listar e desinstalar charts em clusters Kubernetes.
    
    - Helm → É uma forma de instalar de determinados softwares, pacotes, sem a necessidade de instalar manualmente.
    - Imagine que o Helm seria um apt-get da vida
    - Existem algumas formas de se instalar o helm
    
    1. Via snap
        
        ```yaml
        snap install helm --classic
        ```
        
    
    1. Via wget
        
        ```bash
        wget https://storage.googleapis.com/kubernetes-helm/helm-v2.12.3-linux-amd64.tar.gz
        tar -vxzf helm-v2.11.0-linux-amd64.tar.gz
        
        cd linux-amd64/
        
        mv helm /usr/local/bin/
        
        mv tiller /usr/local/bin/
        
        helm
        ```
        
    
    1. Via brew
        
        ```bash
        brew install helm
        ```
        
    
     Para quem esta utilizando o kubeadm, é preciso se atentar com relação ao rbac, ou seja,  o helm precisa estar associado ao **clusterrollebinding**
    
    Siga o passo-a-passo:
    
    ```bash
    create serviceaccount --namespace=kube-system tiller
    kubrctl create clusterrolebinding tiller-cluster-role --clusterrole cluster-admin --serviceaccount=kube-system:tiller
    
    kubectl patch deployments -n kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
    ```
    
    **Com o helm instalado, agora instale o prometheus como exemplo:**
    
    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm install prometheus prometheus-community/prometheus --version 25.8.0
    ```
    
    Assim que o promethueus for instalado no cluster, o tipo de serviço dele será um clusterIp, vá até o service do prometheus-server e dê um edit e edite o tipo de service em type.
    
    ```bash
    kubectl edit service 
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2044.png)
    
    Após editar, siga o passo a passo abaixo:
    
    ```bash
    kubectl get service
    
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2045.png)
    
    ```bash
    # pegue o ip do NÓ de controle
    https://ip:31245 -> digite isso na url de seu navegador
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2046.png)
    
    **Agora suba um deployment para que o prometheus pegue as informações de métricas desse container:**
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: giropops-v1
      labels:
        app: giropops
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: giropops
      template:
        metadata:
          labels:
            app: giropops
          # passando informação para o cluster que o pod pode ser monitorado pelo prometheus
          annotations:
            # Vai começar a pegar informações automaticamente do container que está rodando na porta 8080
            prometheus.io/scrape: "true"
            # Porta que vai expor as métricas
            prometheus.io/port: "32111"
        spec:
          containers:
          - name: giropops
            image: linuxtips/nginx-prometheus-exporter:1.0.0
            env:
            - name: VERSION
              value: "1.0.0"
            ports:
            - containerPort: 80
            - containerPort: 32111
    ```
    
    **Agora clique em Status > Target, e verifique se encontra o pod que subimos**
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2047.png)
    
    **Entre no pod e dê um curl dentro so POD para verificar as métricas do container**
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2048.png)
    
    **Vá na parte inicial do Prometheus e digite: nginx_http_requests**
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2049.png)
    
    **Instalando o Grafana no cluster via Helm**
    
    ```bash
    helm repo add grafana https://grafana.github.io/helm-charts
    helm install my-grafana grafana/grafana --version 7.0.9
    ```
    
    **Usuário e senha padrão do grafana**
    
    ```bash
    
    kubectl get secret --namespace monitoring my-grafana -o jsonpath="{.data.admin-user}" | base64 --decode ; echo
    kubectl get secret --namespace monitoring my-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
    ```
    
    Troque o tipo de Service do grafana por NodePort
    
    **Adicionando um data source no Grafana**
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2050.png)
    
    Criando um Dashboard
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2051.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2052.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2053.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2054.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2055.png)
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2056.png)
    
    **Comandos Helm:**
    
    ```yaml
    # Listar os repositórios
    helm repo list
    helm delete <nome>
    
    helm delete <nome> --purge # Vai deletar de formar completa
    
    # Remover um repositório específico (substitua "nome-do-repositorio" pelo nome real)
    helm repo remove nome-do-repositorio
    
    # Verificar se o repositório foi removido
    helm repo list
    # desinstalar determinado repo
    helm uninstall my-prometheus
    ```
    
  ## **Ingress**
    
    o `Ingress` é um recurso que gerencia o acesso externo aos serviços dentro de um cluster. Ele fornece um conjunto de regras para rotear o tráfego externo para os serviços internos com base em diferentes critérios, como o nome do host, caminho da URL e outros.
    
    Os principais objetivos do `Ingress` incluem:
    
    1. **Roteamento de Tráfego:** O `Ingress` permite rotear o tráfego externo para serviços específicos com base em regras definidas. Isso é útil quando você tem vários serviços em execução no cluster e deseja direcionar o tráfego com base em URLs específicas.
    2. **Terminação SSL/TLS:** O `Ingress` também pode ser configurado para fornecer terminação SSL/TLS, permitindo que o tráfego externo seja criptografado até o `Ingress` antes de ser roteado para os serviços internos.
    3. **Balanceamento de Carga:** Pode ser utilizado em conjunto com controladores de balanceamento de carga para distribuir o tráfego de entrada entre os pods dos serviços.
    
    Para usar o `Ingress`, é necessário ter um controlador de `Ingress` implantado no cluster. Controladores populares incluem o Nginx Ingress Controller e o Traefik. O controlador é responsável por implementar as regras definidas nos recursos `Ingress` e gerenciar o roteamento do tráfego.
    
    - O ingress é uma maneira de abstrair os services
    - Fazer com que os services sejam expostos para além do cluster
    - Fazer com que os serviços rodem fora do cluster
    
    Aqui está um exemplo simples de um recurso `Ingress`:
    
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: meu-ingress
    spec:
      rules:
      - host: meuservico.minhadomina.com
        http:
          paths:
          - path: /caminho1
            pathType: Prefix
            backend:
              service:
                name: servico1
                port:
                  number: 80
          - path: /caminho2
            pathType: Prefix
            backend:
              service:
                name: servico2
                port:
                  number: 8080
    
    ```
    
    Neste exemplo, o tráfego para `meuservico.minhadomina.com/caminho1` será roteado para o serviço chamado `servico1`, enquanto o tráfego para `meuservico.minhadomina.com/caminho2` será roteado para o serviço chamado `servico2`.
    
    Comando para pegar vários Objetos
    
    ```bash
    kubectl get deploy,svc,ep,po
    ```
    
    **CRIANDO UM CLUSTER K8S COM NGINX INGRESS CONTROLLER EM SUA MÁQUINA**
    
    ```yaml
    kind: Cluster
    apiVersion: kind.x-k8s.io/v1alpha4
    nodes:
    - role: control-plane
      kubeadmConfigPatches:
      - |
        kind: InitConfiguration
        nodeRegistration:
          kubeletExtraArgs:
            node-labels: "ingress-ready=true"
      extraPortMappings:
      - containerPort: 80
        hostPort: 80
        protocol: TCP
      - containerPort: 443
        hostPort: 443
        protocol: TCP
    ```
    
    - `kind: Cluster`: Indica que este é um arquivo de configuração para criar um cluster Kind.
    - `apiVersion: kind.x-k8s.io/v1alpha4`: Especifica a versão da API Kind a ser usada.
    - `nodes`: Lista os nós do cluster. No caso, há um nó.
        - `role: control-plane`: Define que este nó será um nó de plano de controle, o que significa que terá todos os componentes principais do Kubernetes.
        - `kubeadmConfigPatches`: Permite aplicar patches à configuração do `kubeadm` usado para inicializar o nó. No caso, um patch é aplicado à seção `InitConfiguration` para adicionar argumentos extras ao `kubelet`. Este patch adiciona um rótulo (`ingress-ready=true`) ao nó.
        - `extraPortMappings`: Mapeia portas adicionais entre o host e o contêiner.
            - `containerPort: 80`: Porta do contêiner a ser mapeada.
            - `hostPort: 80`: Porta do host à qual a porta do contêiner será mapeada.
            - `protocol: TCP`: Protocolo a ser usado para o mapeamento de porta.
            - O mesmo padrão é repetido para a porta 443.
    
    Criando um ingress nginx controller
    
    ```yaml
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2057.png)
    
    Os manifestos contêm patches específicos do tipo para encaminhar os hostPorts para o controlador de ingresso, definir tolerâncias de contaminação e agendá-los para o nó com rótulo personalizado.
    
    Agora o Ingress está todo configurado. Aguarde até que esteja pronto para processar as solicitações em execução:
    
    ```yaml
    kubectl wait --namespace ingress-nginx \
      --for=condition=ready pod \
      --selector=app.kubernetes.io/component=controller \
      --timeout=90s
    ```
    
    YAML testando o ingress
    
    ```yaml
    kind: Pod
    apiVersion: v1
    metadata:
      name: yoda-app
      labels:
        app: yoda
    spec:
      containers:
      - command:
        - /agnhost
        - netexec
        - --http-port
        - "8080"
        image: registry.k8s.io/e2e-test-images/agnhost:2.39
        name: yoda-app
    ---
    kind: Service
    apiVersion: v1
    metadata:
      name: yoda-service
    spec:
      selector:
        app: yoda
      ports:
      # Default port used by the image
      - port: 8080
    ---
    kind: Pod
    apiVersion: v1
    metadata:
      name: dioday-app
      labels:
        app: dioday
    spec:
      containers:
      - command:
        - /agnhost
        - netexec
        - --http-port
        - "8080"
        image: registry.k8s.io/e2e-test-images/agnhost:2.39
        name: dioday-app
    ---
    kind: Service
    apiVersion: v1
    metadata:
      name: dioday-service
    spec:
      selector:
        app: dioday
      ports:
      # Default port used by the image
      - port: 8080
    ---
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: example-ingress
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /$2
    spec:
      rules:
      - http:
          paths:
          - pathType: Prefix
            path: /yoda
            backend:
              service:
                name: yoda-service
                port:
                  number: 8080
          - pathType: Prefix
            path: /dioday
            backend:
              service:
                name: dioday-service
                port:
                  number: 8080
    ```
    
    Quando tudo estiver rodando, faça os seguinte testes com curl
    
    ```bash
    curl localhost/yoda
    curl localhost/dioday
    ```
    
    ![Untitled](Kubernetes%20730e37146baa485d9f46957707434f50/Untitled%2058.png)
    
  ## Assuntos-Extra
    
    ## **Comandos Extras**
    
    ```bash
    kubeadm token create --print-join-command
    ```
    
    **Fazer um autocomplete do kubectl**
    
    ```bash
    kubectl completion bash > /etc
    ```
    
    **Mas primeiro, veja se esta instalado o bash-completion, caso nao esteja instalado, instale com o seguinte comando:**
    
    ```bash
    apt-get install -y bash-completion
    ```
    
    **Redirecionar o completion para o etc**
    
    ```bash
    kubectl completion bash > /etc/bash_completion
    ```
    
    **Forçando ele ler e atualizar a modificacao**
    
    ```bash
    source <(kubectl completion bash)
    ```
    
    ---
    
    **COMANDOS BASICOS**
    
    - Um pod e como se fosse uma jaula onde os container compartilham do mesmo namespace e do mesmo IP do POD
    - namespace -> e um lugar isolado, cercadinho da aplicacao
    
    ```bash
    kubectl get pods
    kubectl get pods -n kube-system
    ```
    
    ```bash
    kubectl describe pods -n kube-system <nomePod>
    kubectl get pods --all-namespace
    kubectl get pods --all-namespace -o wide
    ```
    
    ```bash
    kubectl get namespaces
    ```
    
    ---
    
    **Entrando dentro de determinado Pod e instalando o Vim dentro do container do nginx**
    
    ```bash
    kubectl exec -it meu-nginx-d9cd787bb-6qw92 -- /bin/bash
    
    # ==========================================================
    
    # Dentro do Container
    apt update
    apt install vim
    
    # Va ate o caminho do index.html e altere-o
    root@meu-nginx-d9cd787bb-6qw92:/usr/share/nginx/html# vim index.html
    ```
    
    ## **Conteúdo complementar - EstudosKubernetes**
    
    [https://github.com/Erick-Fernandes-dev/EstudosKubernetes](https://github.com/Erick-Fernandes-dev/EstudosKubernetes)


## HPA

### Instalando o metrics server

MetricServer → Coletar as métricas em tempo real de quanto cada Pod, cada parte do Kubernetes está consumindo naquele momento

Não vêm instaldo no kind por padrão

Link para instalar o metrics-server

[https://github.com/kubernetes-sigs/metrics-server](https://github.com/kubernetes-sigs/metrics-server)

Baixe component: 👇🏻

```go
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

vá em issues: 👇🏻

[https://github.com/kubernetes-sigs/metrics-server/issues/525](https://github.com/kubernetes-sigs/metrics-server/issues/525)

![Untitled](Kubernetes%2076b569f94cdc4598a04fae48c3ab5045/Untitled.png)

Manifesto do metrics server

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
    rbac.authorization.k8s.io/aggregate-to-edit: "true"
    rbac.authorization.k8s.io/aggregate-to-view: "true"
  name: system:aggregated-metrics-reader
rules:
- apiGroups:
  - metrics.k8s.io
  resources:
  - pods
  - nodes
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
rules:
- apiGroups:
  - ""
  resources:
  - nodes/metrics
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - pods
  - nodes
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server-auth-reader
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: extension-apiserver-authentication-reader
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server:system:auth-delegator
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:auth-delegator
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:metrics-server
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: v1
kind: Service
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  ports:
  - name: https
    port: 443
    protocol: TCP
    targetPort: https
  selector:
    k8s-app: metrics-server
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.k8s.io/metrics-server/metrics-server:v0.6.4
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
        - containerPort: 4443
          name: https
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /readyz
            port: https
            scheme: HTTPS
          initialDelaySeconds: 20
          periodSeconds: 10
        resources:
          requests:
            cpu: 100m
            memory: 200Mi
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
        volumeMounts:
        - mountPath: /tmp
          name: tmp-dir
      nodeSelector:
        kubernetes.io/os: linux
      priorityClassName: system-cluster-critical
      serviceAccountName: metrics-server
      volumes:
      - emptyDir: {}
        name: tmp-dir
---
apiVersion: apiregistration.k8s.io/v1
kind: APIService
metadata:
  labels:
    k8s-app: metrics-server
  name: v1beta1.metrics.k8s.io
spec:
  group: metrics.k8s.io
  groupPriorityMinimum: 100
  insecureSkipTLSVerify: true
  service:
    name: metrics-server
    namespace: kube-system
  version: v1beta1
  versionPriority: 100
```

Listar os apiservices

```yaml
kubectl get apiservices
```

### Entendendo utilização de Resources

vCPU → 1000m  (milicores) 500m. 0.5 → metade da minha vCPU

Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: wesleywillians/hello-go:v9.6
        resources:
          requests:
            memory: "5Mi"
            cpu: "0.1"
          limits:
            memory: "10Mi"
            cpu: "0.2"
        ports:
        - containerPort: 8000
```

Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 8000
```

Rodando o Fortio

```yaml
kubectl run --image=fortio/fortio fortio -- load -qps 800 -t 20s -c 50 "http://myapp-service/healthz"
```

Estressando mais ainda

```yaml
kubectl run --image=fortio/fortio fortio -- load -qps 900 -t 20s -c 80 "http://myapp-service/healthz"
```

```yaml
watch -n1 kubectl top pod myapp-599756b6c7-vdh5w
```

### Finalmente o HPA

O HPA (Horizontal Pod Autoscaler) no Kubernetes é um recurso que automatiza a escala do número de réplicas de um conjunto de pods (replica set, deployment, stateful set, etc.) com base na carga de trabalho ou métricas específicas. O objetivo é garantir que sua aplicação tenha a capacidade computacional necessária para lidar com as demandas variáveis.

A principal finalidade do HPA é permitir que sua aplicação se ajuste dinamicamente ao tráfego, escalando para cima quando há um aumento na carga e para baixo quando a carga diminui. Aqui estão alguns pontos-chave sobre o HPA:

1. **Autoscaling com Base em Métricas:**
    - O HPA monitora métricas específicas, como uso de CPU ou métricas personalizadas, e ajusta o número de réplicas do conjunto de pods com base nessas métricas.
2. **Configuração Flexível:**
    - Você pode configurar o HPA para escalar com base em métricas de uso de CPU, uso de memória ou até mesmo métricas personalizadas definidas por você.
3. **Suporte a Métricas Personalizadas:**
    - Além das métricas padrão do sistema, o HPA suporta métricas personalizadas, permitindo que você ajuste a escala com base em aspectos específicos de sua aplicação.
4. **Integração com Múltiplos Tipos de Workloads:**
    - O HPA pode ser utilizado com diferentes tipos de workloads, incluindo deployments, stateful sets e replica sets.
5. **Gradação de Escala:**
    - O HPA permite que você defina regras para escalonamento gradual, evitando ajustes bruscos e proporcionando uma adaptação mais suave às mudanças na carga.
6. **Prevenção contra Oscilação:**
    - O HPA inclui mecanismos para evitar oscilações frequentes de escalonamento, o que ajuda a estabilizar a operação em situações de carga flutuante.
7. **Integração com o Cluster Autoscaler:**
    - Quando usado em conjunto com o Cluster Autoscaler, o HPA pode escalar não apenas o número de pods em um deployment, mas também escalar o número de nós no cluster, proporcionando uma escalabilidade ainda mais abrangente.

Ao usar o HPA, você pode garantir que sua aplicação tenha recursos suficientes para lidar com variações na carga de trabalho, garantindo eficiência operacional e uma experiência mais consistente para os usuários finais.

YAML HPA do deployment myapp

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  # Vai fazer o autoscaling no deployment myapp
  scaleTargetRef:
    apiVersion: apps/v1
    name: myapp
    kind: Deployment
  # vai rodar 1 replica no minimo e 15 no maximo
  minReplicas: 1
  maxReplicas: 30
  # porcentagem de uso de cpu para escalar
  targetCPUUtilizationPercentage: 50
```

```bash
watch -n1 kubectl get hpa
```

O comando `watch` é uma ferramenta de linha de comando que executa periodicamente um comando específico e exibe a saída em tempo real. No caso do comando que você forneceu:

```bash
watch -n1 kubectl get hpa

```

- `watch`: Inicia a execução do comando de forma repetida em intervalos regulares.
- `n1`: Define o intervalo de atualização, indicando que o comando deve ser executado a cada 1 segundo.
- `kubectl get hpa`: É o comando Kubernetes que exibe informações sobre os Autoscalers Horizontais (Horizontal Pod Autoscalers ou HPAs) no cluster.

Portanto, esse comando específico está sendo utilizado para monitorar e exibir as informações sobre os Autoscalers Horizontais no seu cluster Kubernetes. A saída será atualizada a cada segundo, permitindo que você observe alterações dinâmicas nos valores do HPA, como a contagem atual de réplicas, métricas de uso de recursos, entre outras informações relacionadas ao dimensionamento automático. Esse tipo de monitoramento em tempo real pode ser útil para entender como o escalonamento automático está respondendo às mudanças na carga de trabalho do cluster.

## Statefulset

O Statefulset é uma funcionalidade no kubernetes que gerencia o deployment e o scaling de um conjunto de Pods, fornecendo garantias sobre a ordem de deployment e a singularidade desses Pods.

É distinto entre os Deployments e Replicasets que são considerados stateless (sem estado), os `Statefulsets` são utilizados quando você precisa de mais garantias sobre o deployment e scaling. Eles garantem que os nomes e endereços dos Pods sejam consistentes e estáveis ao longo do tempo.

Diferentemente de `Deployments`, que são usados para aplicativos sem estado, `StatefulSets` são ideais para aplicações que mantêm um estado, como bancos de dados e sistemas de armazenamento. Cada instância do pod em um `StatefulSet` tem um nome único e persistente, e elas são criadas e escaladas de maneira sequencial.

### Quando usar StatefulSets?

Os `StatefulSets` são úteis para aplicações que necessitam de um ou mais dos seguintes:

- Identidade de rede estável e única.
- Armazenamento persistente estável.
- Ordem de deployment e scaling garantida.
- Ordem de rolling updates e rollbacks garantida.
- Algumas aplicações que se encaixam nesses requisitos são bancos de
dados, sistemas de filas e quaisquer aplicativos que necessitam de
persistência de dados ou identidade de rede estável.

---

- **dentidade persistente:** Cada pod em um `StatefulSet` possui um nome persistente que é baseado em um padrão, geralmente no formato `<nome-do-statefulset>-<ordinal>`. Essa identidade persistente é mantida mesmo durante reinicializações ou reimplantações.
- **Persistência de armazenamento:** `StatefulSets` suportam volumes persistentes, o que significa que os dados podem ser mantidos mesmo se um pod for movido para outro nó ou se for reiniciado.
- **Ordem de inicialização garantida:** A inicialização dos pods em um `StatefulSet` é feita de forma sequencial, garantindo que cada pod anterior esteja em execução e pronto antes que o próximo seja iniciado. Isso é útil para aplicações que dependem da ordem durante a inicialização.
- **Serviços de cabeçalho:** `StatefulSets` são normalmente associados a serviços de cabeçalho (headless services), que fornecem uma resolução DNS estável para cada pod, facilitando a descoberta de outros pods no conjunto.
- **Atualizações controladas:** Atualizações em um `StatefulSet` podem ser controladas para garantir a estabilidade da aplicação. Isso é especialmente útil para bancos de dados, onde é necessário garantir a consistência dos dados durante atualizações.
    
    
    ### E como ele funciona?
    
    Os `StatefulSets` funcionam criando uma série de Pods 
    replicados. Cada réplica é uma instância da mesma aplicação que é criada
     a partir do mesmo spec, mas pode ser diferenciada por seu índice e 
    hostname.
    
    Ao contrário dos Deployments e Replicasets, onde as réplicas são 
    intercambiáveis, cada Pod em um StatefulSet tem um índice persistente e 
    um hostname que se vinculam a sua identidade.
    
    Por exemplo, se um StatefulSet tiver um nome giropops e um spec com 
    três réplicas, ele criará três Pods: giropops-0, giropops-1, giropops-2.
     A ordem dos índices é garantida. O Pod giropops-1 não será iniciado até
     que o Pod giropops-0 esteja disponível e pronto.
    
    A mesma garantia de ordem é aplicada ao scaling e aos updates.
    
    ### O StatefulSet e os volumes persistentes
    
    Um aspecto chave dos `StatefulSets` é a integração com 
    Volumes Persistentes. Quando um Pod é recriado, ele se reconecta ao 
    mesmo Volume Persistente, garantindo a persistência dos dados entre as 
    recriações dos Pods.
    
    Por padrão, o Kubernetes cria um PersistentVolume para cada Pod em um
     StatefulSet, que é então vinculado a esse Pod para a vida útil do 
    StatefulSet.
    
    Isso é útil para aplicações que precisam de um armazenamento persistente e estável, como bancos de dados.
    
    ### O StatefulSet e os volumes persistentes
    
    Um aspecto chave dos `StatefulSets` é a integração com 
    Volumes Persistentes. Quando um Pod é recriado, ele se reconecta ao 
    mesmo Volume Persistente, garantindo a persistência dos dados entre as 
    recriações dos Pods.
    
    Por padrão, o Kubernetes cria um PersistentVolume para cada Pod em um
     StatefulSet, que é então vinculado a esse Pod para a vida útil do 
    StatefulSet.
    
    Isso é útil para aplicações que precisam de um armazenamento persistente e estável, como bancos de dados.
    
    ### O StatefulSet e o Headless Service
    
    Para entender a relação entre o StatefulSet e o Headless Service, é preciso primeiro entender o que é um Headless Service.
    
    No Kubernetes, um serviço é uma abstração que define um conjunto 
    lógico de Pods e uma maneira de acessá-los. Normalmente, um serviço tem 
    um IP e encaminha o tráfego para os Pods. No entanto, um Headless 
    Service é um tipo especial de serviço que não tem um IP próprio. Em vez 
    disso, ele retorna diretamente os IPs dos Pods que estão associados a 
    ele.
    
    Agora, o que isso tem a ver com os `StatefulSets`?
    
    Os `StatefulSets` e os `Headless Services` geralmente trabalham juntos no gerenciamento de aplicações stateful. O `Headless Service` é responsável por permitir a comunicação de rede entre os Pods em um `StatefulSet`, enquanto o ` gerencia o deployment e o scaling desses Pods.
    
    Aqui está como eles funcionam juntos:
    
    Quando um `StatefulSet` é criado, ele geralmente é associado a um `Headless Service`. Ele é usado para controlar o domínio DNS dos `Pods` criados pelo `StatefulSet`. Cada `Pod` obtém um nome de host DNS que segue o formato: `<pod-name>.<service-name>.<namespace>.svc.cluster.local`. Isso permite que cada `Pod` seja alcançado individualmente.
    
    Por exemplo, se você tiver um `StatefulSet` chamado giropops com três réplicas e um `Headless Service` chamado `nginx`, os `Pods`
     criados serão giropops-0, giropops-1, giropops-2 e eles terão os 
    seguintes endereços de host DNS: 
    
    giropops-0.nginx.default.svc.cluster.local, 
    giropops-1.nginx.default.svc.cluster.local, 
    giropops-2.nginx.default.svc.cluster.local.
    
    Essa combinação de `StatefulSets` com `Headless Services` permite que aplicações `stateful`,
     como bancos de dados, tenham uma identidade de rede estável e 
    previsível, facilitando a comunicação entre diferentes instâncias da 
    mesma aplicação.
    
    ### Criando um StatefulSet:
    
    ```yaml
    apiVersion: apps/v1
    kind: StatefulSet # Tipo do recurso que estamos criando, no caso, um StatefulSet
    metadata:
      name: nginx
    spec:
      serviceName: "nginx"
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx
            ports:
            - containerPort: 80
              name: web
            volumeMounts:
            - name: www
              mountPath: /usr/share/nginx/html
      volumeClaimTemplates: # Como estamos utilizando StatefulSet, precisamos criar um template de volume para cada Pod, entã ao invés de criarmos um volume diretamente, criamos um template que será utilizado para criar um volume para cada Pod
      - metadata:
          name: www # Nome do volume, assim teremos o volume www-0, www-1 e www-2
        spec:
          accessModes: [ "ReadWriteOnce" ]
          resources:
            requests:
              storage: 1Gi
    ```
    
    ```yaml
    kubectl apply -f nginx-statefulset.yaml
    
    # Ver o statefulset
    kubectl get statefulset
    NAME    READY   AGE
    nginx   3/3     2m38s
    
    # vizualizar mais informações
    kubectl describe statefulset nginx
    
    Name:               nginx
    Namespace:          default
    CreationTimestamp:  Thu, 18 May 2023 23:44:45 +0200
    Selector:           app=nginx
    Labels:             <none>
    Annotations:        <none>
    Replicas:           3 desired | 3 total
    Update Strategy:    RollingUpdate
      Partition:        0
    Pods Status:        3 Running / 0 Waiting / 0 Succeeded / 0 Failed
    Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:
          /usr/share/nginx/html from www (rw)
      Volumes:  <none>
    Volume Claims:
      Name:          www
      StorageClass:  
      Labels:        <none>
      Annotations:   <none>
      Capacity:      1Gi
      Access Modes:  [ReadWriteOnce]
    Events:
      Type    Reason            Age   From                    Message
      ----    ------            ----  ----                    -------
      Normal  SuccessfulCreate  112s  statefulset-controller  create Claim www-nginx-0 Pod nginx-0 in StatefulSet nginx success
      Normal  SuccessfulCreate  112s  statefulset-controller  create Pod nginx-0 in StatefulSet nginx successful
      Normal  SuccessfulCreate  102s  statefulset-controller  create Claim www-nginx-1 Pod nginx-1 in StatefulSet nginx success
      Normal  SuccessfulCreate  102s  statefulset-controller  create Pod nginx-1 in StatefulSet nginx successful
      Normal  SuccessfulCreate  96s   statefulset-controller  create Claim www-nginx-2 Pod nginx-2 in StatefulSet nginx success
      Normal  SuccessfulCreate  96s   statefulset-controller  create Pod nginx-2 in StatefulSet nginx successful
    ```
    
    ```yaml
    kubectl get pods
    NAME      READY   STATUS    RESTARTS   AGE
    nginx-0   1/1     Running   0          24s
    nginx-1   1/1     Running   0          14s
    nginx-2   1/1     Running   0          8s
    ```
    
    O nosso `StatefulSet` está criado, mas ainda temos que criar o `Headless Service` para que possamos acessar os `Pods` individualmente, e para isso, vamos criar o arquivo `nginx-headless-service.yaml` com o seguinte conteúdo:
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      ports:
      - port: 80
        name: web
      clusterIP: None # Como estamos criando um Headless Service, não queremos que ele tenha um IP, então definimos o clusterIP como None
      selector:
        app: nginx
    ```
    
    ```yaml
    kubectl apply -f nginx-headless-service.yaml
    
    kubectl get service
    
    kubectl describe service nginx
    
    ```
    
    Agora que já temos o `StatefulSet` e o `Headless Service` criados, podemos acessar cada `Pod` individualmente, para isso, vamos utilizar o comando:
    
    ```yaml
    kubectl run -it --rm debug --image=busybox --restart=Never -- sh
    ```
    
    Agora vamos utilizar o comando `nslookup` para verificar o endereço IP de cada `Pod`, para isso, vamos utilizar o comando:
    
    ```yaml
    nslookup nginx-0.nginx
    ```
    
    Agora vamos acessar o `Pod` utilizando o endereço IP, para isso, vamos utilizar o comando:
    
    ```yaml
    wget -O- http://<endereço-ip-do-pod>
    ```
    
    Precisamos mudar a página web de cada `Pod` para que possamos verificar se estamos acessando o `Pod` correto, para isso, vamos utilizar o comando:
    
    ```
    echo "Pod 0" > /usr/share/nginx/html/index.html
    ```
    
    Agora vamos acessar o `Pod` novamente, para isso, vamos utilizar o comando:
    
    ```
    wget -O- http://<endereço-ip-do-pod>
    ```
    
    A saída do comando deve ser:
    
    ```
    Connecting to <endereço-ip-do-pod>:80 (<endereço-ip-do-pod>:80)
    Pod 0
    ```
    
    Caso queira, você pode fazer o mesmo para os outros `Pods`, basta mudar o número do `Pod` no comando `nslookup` e no comando `echo`.
    
    ### Excluindo um StatefulSet
    
    Para excluir um `StatefulSet` precisamos utilizar o comando:
    
    ```
    kubectl delete statefulset nginx
    
    ```
    
    Ou ainda podemos excluir o `StatefulSet` utilizando o comando:
    
    ```
    kubectl delete -f nginx-statefulset.yaml
    
    ```
    
    ### Excluindo um Headless Service
    
    Para excluir um `Headless Service` precisamos utilizar o comando:
    
    ```
    kubectl delete service nginx
    
    ```
    
    Ou ainda podemos excluir o `Headless Service` utilizando o comando:
    
    ```
    kubectl delete -f nginx-headless-service.yaml
    
    ```
    
    ### Excluindo um PVC
    
    Para excluir um `Volume` precisamos utilizar o comando:
    
    ```
    kubectl delete pvc www-0
    
    ```
    

## Extra

## Comandos:

Entrar dentro de um Container dentro de um Pod:

```yaml
kubectl attach <nome_pod> -c <nome_container> -ti
```

## Arquivos de configuração de Cluster e ingress para KIND 

Este repositório contém arquivos de definição para utilização da ferramenta [KIND](https://kind.sigs.k8s.io/) para criação e gerenciamento dos nossos clusters locais. Possui também um arquivo de configuração referente a implantação do [Ingres](https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx) controller com correções nas definições, para que possamos implantá-lo em nosso ambiente local utilizando o kind e realizar o uso do [Helm](https://helm.sh/) para o controle de versão dos nossos deployments. 

### Passo a Passo 

1 - Criando o cluster local

Vamos criar o noso primeiro cluster, com definições de plugins ( [kubeadmConfigPatches](https://kind.sigs.k8s.io/docs/user/configuration/) ) que possibilitarão a integração e usso do Ingress no kind. Para isso execute o comando. Além disso, mapeamos as portas de entrada do kind com o nosso host.

Para aplicar essa criação use o comando

```
 $ kind create cluster --name kind-1 --config kind-config-cluster.yaml
```

- para listar os clusters após instalação utilize o comando <br>
<pre> kind get clusters </pre>


2 - Criado ingress Controller

Em seguida, vamos implantar o nosso ingress controller e todas as suas dependências utilizando o comando

```
 $ kubectl apply -f kind-ingress-config.yaml
```

Em seguida aguardaremos a implantação completa do nosos ingress controller, para verificarmos essa operação podemos utilizar o comando

```
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s
```

3 - Verificando instalação do ingress

Após os passos anteriores, vamos verificar se o ingress foi adicionado com sucesso listando todos os componentes presentes no namespace do nosso ingress

```
$ kubectl get all -n ingress-nginx 
```

Para detalharmos a execução do pod do nosso controller podemos usar o comando 

```
kubectl describe -n ingress-nginx pod/ingress-nginx-controller-5b8c4489c7-g6xwb
```

O valor do pod pode alterar, logo será necessário alterár o valor <pre>ingress-nginx-controller-5b8c4489c7-g6xwb</pre> de acordo com sua instalação. 

4 - Executando aplicação 

Após terminar os passos acima, para executar a nossa aplicação no cluster criado e utilizamos o ingress, basta executar o comando:

Primeiro verifique se os arquivos não estão corrompidos executando o comando:

```
 $ helm install apivollmed ./chart-voll-med-api/ --dry-run --debug
```
Caso o comando apresente os arquivos de definção, nossa aplicação pode ser instalada com o comando abaixo:

```
 $ helm install apivollmed ./chart-voll-med-api/
```

Com isso o nosso chart será aplicado utilizando os detalhes presentes nos arquivos da pasta /templates


5 - Testando api localmente 

Após a instalação do nosso template, para que possamos testar nossa api precisamos realizar um port-forwading com o nosso ingress controller, para isso utilize o comando:

```
kubectl port-forward service/ingress-nginx-controller -n ingress-nginx 8081:80 --address="0.0.0.0" 
```

Feito isso, poderemos acessar a página da nossa documentação atraves da url: 

http://api.medicos/swagger-ui/index.html

Certifique-se de adicionar o domínio "api.medicos" em seu arquivo <span style="color:red;font-weight:light;">"/etc/hosts"</span>

![DOC](/images/doc_spring.png "Spring Doc")

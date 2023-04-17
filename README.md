## Arquivos de configuração de Cluster e ingress para KIND 

Este repositório contém arquivos de definição para utilização da ferramenta [KIND](https://kind.sigs.k8s.io/) para criação e gerenciamento dos nossos clusters locais. Possui também um arquivo de configuração referente a implantação do [Ingres](https://kind.sigs.k8s.io/docs/user/ingress/#ingress-nginx) controller com correções nas definições, para que possamos implantá-lo em nosso ambiente local utilizando o kind. 

### Passo a Passo

1 - Criando o cluster local

Vamos criar o noso primeiro cluster, com definições de plugins ( [kubeadmConfigPatches](https://kind.sigs.k8s.io/docs/user/configuration/) ) que possibilitarão a integração e usso do Ingress no kind. Para isso execute o comando. Além disso, mapeamos as portas de entrada do kind com o nosso host.

Para aplicar essa criação use o comando

```
 $ kind create cluster --config kind-config-cluster.yaml
```

2 - Criado ingress Controller

Em seguida, vamos implantar o nosso ingress controller e todas as suas dependências utilizando o comando

```
 $ kubectl apply -f kind-ingress-config.yaml
```

Em seguida aguardaremos a implantação completa do nosos ingress controller, para verificarmos essa operação podemos utilizar o comando

```
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s
```

3 - Verificando instalação 

Após os passos anteriores, vamos verificar se o ingress foi adicionado com sucesso listando todos os componentes presentes no namespace do nosso ingress

```
$ kubectl get all -n ingress-nginx 
```

Para detalharmos a execução do pod do nosso controller podemos usar o comando 

```
kubectl describe -n ingress-nginx pod/ingress-nginx-controller-5b8c4489c7-g6xwb
```

O valor do pod pode alterar, logo será necessário alterár o valor <pre>ingress-nginx-controller-5b8c4489c7-g6xwb</pre> de acordo com sua instalação. 





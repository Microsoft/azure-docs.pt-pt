---
title: Perguntas mais frequentes
description: Respostas para perguntas frequentes relacionadas com o serviço de Registo de Contentores Azure
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: a8c007d7f4419ddbe1555b50ceb6fb92ea0a6f98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783904"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Perguntas frequentes sobre o Registo de Contentores Azure

Este artigo aborda perguntas frequentes e questões conhecidas sobre o Registo de Contentores Azure.

Para orientação de resolução de problemas de registo, consulte:
* [Login de registo de resolução de problemas](container-registry-troubleshoot-login.md)
* [Problemas de rede de resolução de problemas com registo](container-registry-troubleshoot-access.md)
* [Desempenho do registo de resolução de problemas](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Gestão de recursos

- [Posso criar um registo de contentores Azure utilizando um modelo de Gestor de Recursos?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Existe vulnerabilidade de segurança à procura de imagens em ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Como posso configurar Kubernetes com registo de contentores Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Como consigo credenciais de administração para um registo de contentores?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Como consigo credenciais de administração num modelo de Gestor de Recursos?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A eliminação da replicação falha com o estatuto de Proibido, embora a replicação seja eliminada usando o Azure CLI ou o Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [As regras de firewall são atualizadas com sucesso, mas não fazem efeito](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Posso criar um registo de contentores Azure utilizando um modelo de Gestor de Recursos?

Sim. Aqui está [um modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) que pode usar para criar um registo.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Existe vulnerabilidade de segurança à procura de imagens em ACR?

Sim. Consulte a documentação do [Azure Security Center](../security-center/defender-for-container-registries-introduction.md), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Como posso configurar Kubernetes com registo de contentores Azure?

Consulte a documentação para [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e passos para [o Serviço Azure Kubernetes](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Como consigo credenciais de administração para um registo de contentores?

> [!IMPORTANT]
> A conta de utilizador administrada foi concebida para que um único utilizador aceda ao registo, principalmente para efeitos de teste. Não recomendamos a partilha das credenciais de conta de administração com vários utilizadores. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça. Consulte [a visão geral da autenticação](container-registry-authentication.md).

Antes de obter credenciais de administração, certifique-se de que o utilizador administrativo do registo está ativado.

Para obter credenciais usando o Azure CLI:

```azurecli
az acr credential show -n myRegistry
```

Com o Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Como consigo credenciais de administração num modelo de Gestor de Recursos?

> [!IMPORTANT]
> A conta de utilizador administrada foi concebida para que um único utilizador aceda ao registo, principalmente para efeitos de teste. Não recomendamos a partilha das credenciais de conta de administração com vários utilizadores. A identidade individual é recomendada para utilizadores e diretores de serviço para cenários sem cabeça. Consulte [a visão geral da autenticação](container-registry-authentication.md).

Antes de obter credenciais de administração, certifique-se de que o utilizador administrativo do registo está ativado.

Para obter a primeira senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Para obter a segunda senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A eliminação da replicação falha com o estatuto de Proibido, embora a replicação seja eliminada usando o Azure CLI ou o Azure PowerShell

O erro é visto quando o utilizador tem permissões num registo, mas não tem permissões ao nível do Leitor na subscrição. Para resolver este problema, atribua permissões do Reader na subscrição ao utilizador:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>As regras de firewall são atualizadas com sucesso, mas não fazem efeito

Leva algum tempo para propagar as mudanças na regra da firewall. Depois de alterar as definições de firewall, aguarde alguns minutos antes de verificar esta alteração.


## <a name="registry-operations"></a>Operações de registo

- [Como acesso ao Registo de Docker HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Como posso eliminar todos os manifestos que não são referenciados por qualquer etiqueta num repositório?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Porque é que o uso da quota de registo não diminui após a eliminação das imagens?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Como valido as alterações da quota de armazenamento?](#how-do-i-validate-storage-quota-changes)
- [Como posso autenticar com o meu registo quando executo o CLI num contentor?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Como ativar o TLS 1.2?](#how-to-enable-tls-12)
- [O Registo de Contentores Azure suporta o Content Trust?](#does-azure-container-registry-support-content-trust)
- [Como posso conceder acesso a imagens de puxar ou empurrar sem permissão para gerir o recurso de registo?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Como posso permitir a quarentena automática de imagem para um registo?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Como devo proceder para ativar o acesso por pedido anónimo?](#how-do-i-enable-anonymous-pull-access)
- [Como empurro camadas não distribuíveis para um registo?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Como acesso ao Registo de Docker HTTP API V2?

A ACR suporta o Registo Estival HTTP API V2. As APIs podem ser acedidas em `https://<your registry login server>/v2/` . Exemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Como posso eliminar todos os manifestos que não são referenciados por qualquer etiqueta num repositório?

Se estiver em festa:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Nota: Pode adicionar `-y` no comando de eliminação para saltar a confirmação.

Para obter mais informações, consulte [Eliminar as imagens dos contentores no Registo do Contentor Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Porque é que o uso da quota de registo não diminui após a eliminação das imagens?

Esta situação pode acontecer se as camadas subjacentes ainda estiverem a ser referenciadas por outras imagens do contentor. Se eliminar uma imagem sem referências, o registo atualiza-se em poucos minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>Como valido as alterações da quota de armazenamento?

Crie uma imagem com uma camada de 1GB utilizando o seguinte ficheiro docker. Isto garante que a imagem tem uma camada que não é partilhada por qualquer outra imagem no registo.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Construa e empurre a imagem para o seu registo utilizando o CLI do estivador.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Você deve ser capaz de ver que o uso de armazenamento aumentou no portal Azure, ou você pode consultar o uso usando o CLI.

```azurecli
az acr show-usage -n myregistry
```

Elimine a imagem utilizando o Azure CLI ou portal e verifique a utilização atualizada em poucos minutos.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Como posso autenticar com o meu registo quando executo o CLI num contentor?

É necessário executar o contentor Azure CLI montando a tomada Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

No recipiente, `docker` instale:

```bash
apk --update add docker
```

Em seguida, autentica com o seu registo:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Como ativar o TLS 1.2?

Ativar o TLS 1.2 utilizando qualquer cliente estivador recente (versão 18.03.0 e superior). 

> [!IMPORTANT]
> A partir de 13 de janeiro de 2020, o Registo de Contentores Azure exigirá todas as ligações seguras dos servidores e aplicações para utilizar o TLS 1.2. O suporte para tLS 1.0 e 1.1 será retirado.

### <a name="does-azure-container-registry-support-content-trust"></a>O Registo de Contentores Azure suporta o Content Trust?

Sim, pode utilizar imagens fidedignas no Registo do Contentor Azure, uma vez que o [Notário Docker](https://docs.docker.com/notary/getting_started/) foi integrado e pode ser ativado. Para mais informações, consulte [content trust no Registo de Contentores Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Onde está o ficheiro para a impressão digital?

Em `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* As chaves públicas e os certificados de todas as funções (exceto as funções de delegação) são armazenados no `root.json` .
* As chaves e certificados públicos da função de delegação são armazenados no ficheiro JSON da sua função-mãe (por `targets.json` exemplo, para o `targets/releases` papel).

Sugere-se que verifique as chaves e certificados públicos após a verificação geral da TUF feita pelo cliente Docker e Notário.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Como posso conceder acesso a imagens de puxar ou empurrar sem permissão para gerir o recurso de registo?

A ACR suporta [funções personalizadas](container-registry-roles.md) que fornecem diferentes níveis de permissões. Especificamente, `AcrPull` e `AcrPush` as funções permitem que os utilizadores puxem e/ou empurrem imagens sem a permissão para gerir o recurso de registo em Azure.

* Portal Azure: O seu registo -> Controlo de Acesso (IAM) -> Adicionar (Selecione `AcrPull` ou `AcrPush` para a Função).
* Azure CLI: Encontre o ID de recurso do registo executando o seguinte comando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Em seguida, pode atribuir a `AcrPull` ou `AcrPush` função a um utilizador (o seguinte exemplo `AcrPull` usa):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Ou, atribuir a função a um chefe de serviço identificado pela sua identificação de aplicação:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

O cessionário é então capaz de autenticar e aceder a imagens no registo.

* Para autenticar a um registo:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para listar repositórios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Para puxar uma imagem:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Com o uso de apenas o `AcrPull` ou `AcrPush` papel, o cessionário não tem a permissão para gerir o recurso de registo em Azure. Por exemplo, `az acr list` ou não mostrar o `az acr show -n myRegistry` registo.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Como posso permitir a quarentena automática de imagem para um registo?

A quarentena de imagem é atualmente uma característica de pré-visualização do ACR. Pode ativar o modo de quarentena de um registo para que apenas as imagens que tenham passado com sucesso na verificação de segurança sejam visíveis para utilizadores normais. Para mais detalhes, consulte o [repo ACR GitHub](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Como devo proceder para ativar o acesso por pedido anónimo?

A criação de um registo de contentores Azure para acesso anónimo (não autenticado) é atualmente uma funcionalidade de pré-visualização, disponível nos [níveis](container-registry-skus.md)de serviço Standard e Premium . 

Para ativar o acesso anónimo, atualize um registo utilizando o CLI Azure (versão 2.21.0 ou posterior) e passe o `--anonymous-pull-enabled` parâmetro para o comando de [atualização az acr:](/cli/azure/acr#az_acr_update)

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

Pode desativar o acesso anónimo a qualquer momento, definindo `--anonymous-pull-enabled` para `false` .

> [!NOTE]
> * Antes de tentar uma operação de atração anónima, corra `docker logout` para garantir que limpe as credenciais existentes do Docker.
> * Apenas as operações de data-plane estão disponíveis para clientes não autenticados.
> * O registo pode acelerar uma elevada taxa de pedidos não autenticados.

> [!WARNING]
> O acesso anónimo aplica-se atualmente a todos os repositórios do registo. Se gerir o acesso ao [repositório utilizando fichas com âmbito de repositório,](container-registry-repository-scoped-permissions.md)esteja ciente de que todos os utilizadores podem retirar desses repositórios num registo habilitado para a atração anónima. Recomendamos a eliminação de fichas quando o acesso anónimo estiver ativado.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Como empurro camadas não distribuíveis para um registo?

Uma camada não distribuível num manifesto contém um parâmetro URL de que o conteúdo pode ser recolhido. Alguns casos de utilização possíveis para permitir emoções de camadas não distribuíveis são para registos restritos de rede, registos com lacunas aéreas com acesso restrito ou para registos sem conectividade com internet.

Por exemplo, se tiver regras NSG configuradas para que um VM possa retirar imagens apenas do seu registo de contentores Azure, Docker irá retirar falhas para camadas estranhas/não distribuíveis. Por exemplo, uma imagem do Núcleo do Servidor do Windows conteria referências de camadas estranhas ao registo de contentores Azure no seu manifesto e não conseguiria puxar neste cenário.

Para permitir empurrar camadas não distribuíveis:

1. Edite o `daemon.json` ficheiro, que está localizado nos `/etc/docker/` anfitriões Linux e `C:\ProgramData\docker\config\daemon.json` no Windows Server. Assumindo que o ficheiro estava anteriormente vazio, adicione o seguinte conteúdo:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > O valor é um conjunto de endereços de registo, separados por vírgulas.

2. Guarde e saia do ficheiro.

3. Reinicie o Docker.

Quando empurra as imagens para os registos da lista, as suas camadas não distribuíveis são empurradas para o registo.

> [!WARNING]
> Os artefactos não distribuíveis normalmente têm restrições sobre como e onde podem ser distribuídos e partilhados. Utilize esta funcionalidade apenas para empurrar artefactos para registos privados. Certifique-se de que está em conformidade com quaisquer termos que cubram a redistribuição de artefactos não distribuíveis.

## <a name="diagnostics-and-health-checks"></a>Diagnósticos e exames de saúde

- [Verifique a saúde com `az acr check-health`](#check-health-with-az-acr-check-health)
- [o estivador falha com erro: net/http: pedido cancelado enquanto aguarda a ligação (Cliente.Timeout excedido enquanto aguarda os cabeçalhos)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [estivador empurra, mas estivador falha com erro: não autorizado: autenticação necessária](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` sucede, mas os comandos de estivador falham com erro: não autorizado: autenticação necessária](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Ativar e obter os registos de depuração do daemon estivador](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [As novas permissões do utilizador podem não ter efeito imediatamente após a atualização](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [As informações de autenticação não são dadas no formato correto nas chamadas diretas da API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Porque é que o portal Azure não lista todos os meus repositórios ou etiquetas?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Porque é que o portal Azure não consegue ir buscar repositórios ou etiquetas?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Porque é que o meu pedido de puxar ou empurrar falha com uma operação proibida?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [O formato repositório é inválido ou não suportado](#repository-format-is-invalid-or-unsupported)
- [Como posso recolher vestígios de http no Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Verifique a saúde com `az acr check-health`

Para resolver problemas comuns de ambiente e registo, consulte [a saúde de um registo de contentores Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>o estivador falha com erro: net/http: pedido cancelado enquanto aguarda a ligação (Cliente.Timeout excedido enquanto aguarda os cabeçalhos)

 - Se este erro for um problema transitório, então a repetição será bem sucedida.
 - Se `docker pull` falhar continuamente, pode haver um problema com o daemon do Docker. O problema pode geralmente ser atenuado reiniciando o daemon Docker. 
 - Se continuar a ver este problema depois de reiniciar o Daemon do Docker, então o problema pode ser alguns problemas de conectividade da rede com a máquina. Para verificar se a rede geral da máquina está saudável, executar o seguinte comando para testar a conectividade do ponto final. A versão mínima `az acr` que contém este comando de verificação de conectividade é 2.2.9. Atualize o seu Azure CLI se estiver a utilizar uma versão mais antiga.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Deves ter sempre um mecanismo de repetição em todas as operações dos clientes do Docker.

### <a name="docker-pull-is-slow"></a>Docker pull é lento
Utilize [esta](http://www.azurespeed.com/Azure/Download) ferramenta para testar a velocidade de descarregamento da rede de máquinas. Se a rede de máquinas for lenta, considere utilizar o Azure VM na mesma região que o seu registo. Isto geralmente dá-lhe uma velocidade de rede mais rápida.

### <a name="docker-push-is-slow"></a>O empurrão do Docker é lento.
Utilize [esta](http://www.azurespeed.com/Azure/Upload) ferramenta para testar a velocidade de carregamento da rede de máquinas. Se a rede de máquinas for lenta, considere utilizar o Azure VM na mesma região que o seu registo. Isto geralmente dá-lhe uma velocidade de rede mais rápida.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push sucede mas estivador falha com erro: não autorizado: autenticação necessária

Este erro pode acontecer com a versão Red Hat do daemon Docker, onde `--signature-verification` é ativado por padrão. Pode verificar as opções do Daemon do Docker para Red Hat Enterprise Linux (RHEL) ou Fedora executando o seguinte comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por exemplo, o Fedora 28 Server tem as seguintes opções de daemon docker:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Com `--signature-verification=false` falta, `docker pull` falha com um erro semelhante a:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver o erro:
1. Adicione a opção `--signature-verification=false` ao ficheiro de configuração do Daemon Doemon `/etc/sysconfig/docker` Doemon . Por exemplo:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Reinicie o serviço Daemon Docker executando o seguinte comando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Os detalhes `--signature-verification` podem ser encontrados correndo. `man dockerd`

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login sucede mas docker falha com erro: não autorizado: autenticação necessária

Certifique-se de que utiliza um URL de servidor minúsculo, por exemplo, `docker push myregistry.azurecr.io/myimage:latest` mesmo que o nome do recurso de registo seja maiúscula ou mista, como `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Ativar e obter os registos de depuração do daemon Docker    

Comece `dockerd` com a `debug` opção. Primeiro, crie o ficheiro de configuração do Daemon Doemon Doemon ( `/etc/docker/daemon.json` ) se não existir, e adicione a `debug` opção:

```json
{    
    "debug": true    
}
```

Então, reinicie o daemon. Por exemplo, com Ubuntu 14.04:

```bash
sudo service docker restart
```

Os detalhes podem ser encontrados na documentação do [Docker.](https://docs.docker.com/engine/admin/#enable-debugging)    

 * Os registos podem ser gerados em diferentes locais, dependendo do seu sistema. Por exemplo, para Ubuntu 14.04, é `/var/log/upstart/docker.log` .    
Consulte [a documentação do Docker](https://docs.docker.com/engine/admin/#read-the-logs) para mais detalhes.    

 * Para o Docker para windows, os registos são gerados em %LOCALAPPDATA%/docker/. No entanto, pode ainda não conter todas as informações de depurg.    

   Para aceder ao registo completo do daemon, poderá necessitar de alguns passos extras:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Agora tem acesso a todos os ficheiros do VM em `dockerd` execução. O registo está em `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>As novas permissões do utilizador podem não ter efeito imediatamente após a atualização

Quando concede novas permissões (novas funções) a um diretor de serviço, a alteração pode não entrar em vigor imediatamente. Existem duas razões possíveis:

* Atraso na atribuição do papel do Azure Ative Directory. Normalmente é rápido, mas pode levar minutos devido ao atraso de propagação.
* Atraso de permissão no servidor de fichas ACR. Isto pode levar até 10 minutos. Para atenuar, pode `docker logout` e depois autenticar novamente com o mesmo utilizador após 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Atualmente, a ACR não suporta a eliminação de replicação domiciliária por parte dos utilizadores. A solução é incluir a replicação da casa criar no modelo, mas saltar a sua criação adicionando `"condition": false` como mostrado abaixo:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>As informações de autenticação não são dadas no formato correto nas chamadas diretas da API REST

Pode encontrar um `InvalidAuthenticationInfo` erro, especialmente utilizando a `curl` ferramenta com a opção , `-L` `--location` (para seguir redirecionamentos).
Por exemplo, buscar a bolha utilizando `curl` com `-L` opção e autenticação básica:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

pode resultar na seguinte resposta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A causa principal é que `curl` algumas implementações seguem redirecionamentos com cabeçalhos do pedido original.

Para resolver o problema, é necessário seguir os redirecionamentos manualmente sem os cabeçalhos. Imprima os cabeçalhos de resposta com a `-D -` opção de `curl` e, em seguida, extrair: o `Location` cabeçalho:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Porque é que o portal Azure não lista todos os meus repositórios ou etiquetas? 

Se estiver a utilizar o navegador Microsoft Edge/IE, pode ver no máximo 100 repositórios ou tags. Se o seu registo tiver mais de 100 repositórios ou tags, recomendamos que utilize o navegador Firefox ou Chrome para listar todos.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Porque é que o portal Azure não consegue ir buscar repositórios ou etiquetas?

O navegador pode não ser capaz de enviar o pedido de reenervação de repositórios ou tags para o servidor. Pode haver várias razões, tais como:

* Falta de conectividade de rede
* Firewall
* Bloqueadores de anúncios
* Erros do DNS

Entre em contato com o administrador de rede ou verifique a configuração e conectividade da sua rede. Tente executar `az acr check-health -n yourRegistry` o seu Azure CLI para verificar se o seu ambiente é capaz de se ligar ao Registo de Contentores. Além disso, você também pode experimentar uma sessão incógnita ou privada no seu navegador para evitar qualquer cache ou cookies de navegador.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Porque é que o meu pedido de puxar ou empurrar falha com uma operação proibida?

Eis alguns cenários em que as operações podem ser proibidas:
* Os registos clássicos já não são suportados. Por favor, atualize para um [nível de serviço](./container-registry-skus.md) suportado usando [a atualização az acr](/cli/azure/acr#az_acr_update) ou o portal Azure.
* A imagem ou repositório talvez bloqueado para que não possa ser apagado ou atualizado. Pode utilizar o comando [de repositório az acr show](./container-registry-image-lock.md) para visualizar os atributos atuais.
* Algumas operações são proibidas se a imagem estiver em quarentena. Saiba mais sobre [a quarentena.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)
* O seu registo pode ter atingido o [limite de armazenamento.](container-registry-skus.md#service-tier-features-and-limits)

### <a name="repository-format-is-invalid-or-unsupported"></a>O formato repositório é inválido ou não suportado

Se vir um erro como "formato repositório não suportado", "formato inválido", ou "os dados solicitados não existem" ao especificar um nome de repositório em operações de repositório, verifique a ortografia e o caso do nome. Os nomes de repositórios válidos só podem incluir caracteres alfanuméricos minúsculos, períodos, traços, sublinhados e cortes para a frente. 

Para obter regras completas de nomeação do repositório, consulte a [Especificação de Distribuição](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview)da Iniciativa de Recipiente Aberto .

### <a name="how-do-i-collect-http-traces-on-windows"></a>Como posso recolher vestígios de http no Windows?

#### <a name="prerequisites"></a>Pré-requisitos

- Ativar a desencriptação https no violinista:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Ativar o Docker a usar um representante através do Docker ui: <https://docs.docker.com/docker-for-windows/#proxies>
- Certifique-se de reverter quando estiver completo.  O Docker não vai trabalhar com isto ativado e o violinista não vai funcionar.

#### <a name="windows-containers"></a>Contentores do Windows

Configure o proxy do Docker para 127.0.0.1:8888

#### <a name="linux-containers"></a>Contentores do Linux

Encontre a ip do interruptor virtual Docker vm:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configure o representante do Docker para a saída do comando anterior e da porta 8888 (por exemplo 10.0.75.1:8888)

## <a name="tasks"></a>Tarefas

- [Como é que cancelo as corridas?](#how-do-i-batch-cancel-runs)
- [Como incluo a pasta .git no comando de construção az acr?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [As tarefas suportam o GitLab para os gatilhos de origem?](#does-tasks-support-gitlab-for-source-triggers)
- [Que serviço de gestão de repositório de git suporta tarefas?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Como é que cancelo as corridas?

Os seguintes comandos cancelam todas as tarefas de execução no registo especificado.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Como incluo a pasta .git no comando de construção az acr?

Se passar uma pasta de origem local para o `az acr build` comando, a `.git` pasta é excluída da embalagem carregada por predefinição. Pode criar um `.dockerignore` ficheiro com a seguinte definição. Diz ao comando para restaurar todos os ficheiros `.git` no pacote carregado. 

`!.git/**`

Esta definição também se aplica ao `az acr run` comando.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>As tarefas suportam o GitLab para os gatilhos de origem?

Atualmente, não apoiamos o GitLab para os gatilhos de origem.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Que serviço de gestão de repositório de git suporta tarefas?

| Serviço git | Contexto de origem | Construção manual | Construção automática através do gatilho de comprometimento |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Sim | Yes |
| Repositórios do Azure | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Yes | Yes |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Yes | No |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Yes | No |

## <a name="run-error-message-troubleshooting"></a>Executar resolução de problemas de mensagem de erro

| Mensagem de erro | Guia de resolução de problemas |
|---|---|
|Não foi configurado nenhum acesso para o VM, portanto não foram encontradas assinaturas|Isto pode acontecer se estiver a utilizar `az login --identity` na sua Tarefa ACR. Trata-se de um erro transitório e ocorre quando a atribuição de funções da sua Identidade Gerida não se propagou. Esperando alguns segundos antes de voltar a tentar.|

## <a name="cicd-integration"></a>Integração CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais](container-registry-intro.md) sobre o Registo de Contentores Azure.

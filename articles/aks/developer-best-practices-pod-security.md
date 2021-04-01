---
title: Melhores práticas do desenvolvedor - Pod security in Azure Kubernetes Services (AKS)
description: Aprenda as melhores práticas do desenvolvedor para como proteger pods no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 1c7143b6d3479cf3083cfc730301c68dcf4eb705
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92900826"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Melhores práticas para a segurança do pod no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), a segurança das suas cápsulas é uma consideração fundamental. As suas aplicações devem ser concebidas para o princípio do menor número de privilégios necessários. Manter os dados privados seguros é o topo de mente para os clientes. Não quer credenciais como cadeias de ligação de base de dados, chaves ou segredos e certificados expostos ao mundo exterior onde um intruso possa tirar partido desses segredos para fins maliciosos. Não os adicione ao seu código nem os incorpore nas imagens do seu recipiente. Esta abordagem criaria um risco de exposição e limitaria a capacidade de rodar essas credenciais, uma vez que as imagens dos contentores terão de ser reconstruídas.

Este artigo de boas práticas centra-se em como proteger cápsulas em AKS. Saiba como:

> [!div class="checklist"]
> * Use o contexto de segurança do pod para limitar o acesso a processos e serviços ou escalada de privilégios
> * Autenticar com outros recursos Azure utilizando identidades geridas por pod
> * Solicitar e recuperar credenciais de um cofre digital como Azure Key Vault

Também pode ler as melhores práticas para [a segurança][best-practices-cluster-security] do cluster e para [a gestão da imagem do contentor.][best-practices-container-image-management]

## <a name="secure-pod-access-to-resources"></a>Acesso seguro dos recursos da cápsula

**Orientação de boas práticas** - Para funcionar como um utilizador ou grupo diferente e limitar o acesso aos processos e serviços do nó subjacente, defina as definições de contexto de segurança do pod. Atribua o menor número de privilégios necessários.

Para que as suas aplicações sejam executadas corretamente, as cápsulas devem funcionar como um utilizador ou grupo definido e não como *raiz*. O `securityContext` para uma cápsula ou recipiente permite definir configurações como *runAsUser* ou *fsGroup* para assumir as permissões apropriadas. Apenas atribua as permissões necessárias ao utilizador ou grupo, e não utilize o contexto de segurança como meio para assumir permissões adicionais. As *configurações de runAsUser,* privilege, e outras definições de capacidades Linux só estão disponíveis em nós e pods Linux.

Quando funciona como utilizador não-raiz, os contentores não podem ligar-se às portas privilegiadas abaixo de 1024. Neste cenário, os Serviços Kubernetes podem ser utilizados para disfarçar o facto de uma aplicação estar a funcionar numa determinada porta.

Um contexto de segurança do pod também pode definir capacidades ou permissões adicionais para aceder a processos e serviços. Podem ser definidas as seguintes definições de contexto de segurança comuns:

* **permitir que a MelhorvilegeEscalation** defina se a cápsula pode assumir privilégios *de raiz.* Desenhe as suas aplicações de modo a que esta definição seja sempre definida como *falsa*.
* **As capacidades do Linux** permitem ao pod aceder aos processos subjacentes ao nó. Cuide de atribuir estas capacidades. Atribua o menor número de privilégios necessários. Para mais informações, consulte [as capacidades do Linux.][linux-capabilities]
* **As etiquetas SELinux** são um módulo de segurança do kernel Linux que permite definir políticas de acesso para serviços, processos e acesso ao sistema de ficheiros. Mais uma vez, atribua o menor número de privilégios necessários. Para mais informações, consulte [as opções SELinux em Kubernetes][selinux-labels]

O manifesto do seguinte exemplo do pod YAML define as definições de contexto de segurança para definir:

* Pod funciona como iD *1000* do utilizador e parte do grupo ID *2000*
* Não pode escalar privilégios para usar `root`
* Permite que as capacidades do Linux acedam às interfaces de rede e ao relógio em tempo real (hardware) do anfitrião

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

Trabalhe com o seu operador de cluster para determinar que definições de contexto de segurança necessita. Tente desenhar as suas aplicações para minimizar permissões adicionais e aceder ao pod requer. Existem funcionalidades de segurança adicionais para limitar o acesso usando o AppArmor e o seccomp (computação segura) que podem ser implementados pelos operadores de cluster. Para obter mais informações, consulte [o acesso seguro dos recipientes aos recursos.][apparmor-seccomp]

## <a name="limit-credential-exposure"></a>Limitar a exposição à credencial

**Orientação de boas práticas** - Não defina credenciais no seu código de aplicação. Utilize identidades geridas para os recursos da Azure para permitir que o seu pod solicite acesso a outros recursos. Um cofre digital, como o Azure Key Vault, também deve ser usado para armazenar e recuperar chaves e credenciais digitais. As identidades geridas por pod destinam-se apenas a ser utilizadas apenas com cápsulas Linux e imagens de contentores.

Para limitar o risco de as credenciais serem expostas no seu código de aplicação, evite a utilização de credenciais fixas ou partilhadas. Credenciais ou chaves não devem ser incluídas diretamente no seu código. Se estas credenciais forem expostas, a aplicação tem de ser atualizada e redistribuída. Uma melhor abordagem é dar aos pods a sua própria identidade e forma de se autenticarem, ou recuperarem automaticamente credenciais de um cofre digital.

### <a name="use-azure-container-compute-upstream-projects"></a>Use projetos Azure Container Compute Upstream

> [!IMPORTANT]
> Os projetos de código aberto associados da AKS não são apoiados pelo suporte técnico da Azure. São fornecidos para que os utilizadores se auto-instalem em clusters e recolham feedback da nossa comunidade.

Os [seguintes projetos de código aberto AKS associados][aks-associated-projects] permitem-lhe autenticar automaticamente cápsulas ou solicitar credenciais e chaves a partir de um cofre digital. Estes projetos são mantidos pela equipa Azure Container Compute Upstream e fazem parte de uma [lista mais alargada de projetos disponíveis para utilização.](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support)

 * [Identidade do Pod do Diretório Ativo Azure][aad-pod-identity]
 * [Fornecedor de cofre chave Azure para o motorista csi loja de segredos](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>Use identidades geridas por pod

Uma identidade gerida para os recursos Azure permite que uma cápsula se autente contra os serviços Azure que a suportam, como o Storage ou o SQL. O casulo é atribuído a uma Identidade Azure que permite autenticar para o Azure Ative Directory e receber um token digital. Este token digital pode ser apresentado a outros serviços Azure que verifiquem se a cápsula está autorizada a aceder ao serviço e executar as ações necessárias. Esta abordagem significa que não são necessários segredos para as cadeias de ligação de bases de dados, por exemplo. O fluxo de trabalho simplificado para a identidade gerida em pod é indicado no seguinte diagrama:

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Fluxo de trabalho simplificado para identidade gerida em Azure":::

Com uma identidade gerida, o seu código de aplicação não precisa de incluir credenciais para aceder a um serviço, como o Azure Storage. À medida que cada cápsula autentica com a sua própria identidade, pode auditar e rever o acesso. Se a sua aplicação se ligar a outros serviços Azure, utilize identidades geridas para limitar a reutilização credencial e o risco de exposição.

Para obter mais informações sobre identidades de pod, consulte [configurar um cluster AKS para utilizar identidades geridas por pod e com as suas aplicações][aad-pod-identity]

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>Use a adófita de chave Azure com o motorista CSI da Loja de Segredos

A utilização do projeto de identidade pod permite a autenticação contra o apoio aos serviços Azure. Para os seus próprios serviços ou aplicações sem identidades geridas para recursos Azure, ainda pode autenticar usando credenciais ou chaves. Um cofre digital pode ser usado para armazenar estes conteúdos secretos.

Quando as aplicações precisam de uma credencial, comunicam com o cofre digital, recuperam os conteúdos secretos mais recentes e ligam-se ao serviço necessário. Azure Key Vault pode ser este cofre digital. O fluxo de trabalho simplificado para a recuperação de uma credencial do Azure Key Vault utilizando identidades geridas por cápsulas é mostrado no seguinte diagrama:

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="Fluxo de trabalho simplificado para recuperar uma credencial do Key Vault usando uma identidade gerida por cápsulas":::

Com o Key Vault, armazena e gira regularmente segredos como credenciais, chaves de conta de armazenamento ou certificados. Pode integrar o Azure Key Vault com um cluster AKS utilizando o [fornecedor Azure Key Vault para o Controlador CSI Secrets Store](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage). O controlador CSI Secrets Store permite ao cluster AKS recuperar conteúdos secretos do Key Vault e fornecê-los de forma segura apenas à cápsula de pedido. Trabalhe com o seu operador de cluster para implantar o motorista CSI Secrets Store em nós de trabalhadores AKS. Pode utilizar uma identidade gerida por um pod para solicitar acesso ao Key Vault e recuperar os conteúdos secretos necessários através do Controlador CSI Secrets Store.

Azure Key Vault com Secrets Store CSI Driver pode ser usado para nós e cápsulas Linux que requerem uma versão Kubernetes de 1.16 ou maior. Para os nós e cápsulas windows é necessária uma versão Kubernetes de 1.18 ou superior.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como proteger as suas cápsulas. Para implementar algumas destas áreas, consulte os seguintes artigos:

* [Utilize identidades geridas para recursos Azure com AKS][aad-pod-identity]
* [Integre o cofre da chave Azure com AKS][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources

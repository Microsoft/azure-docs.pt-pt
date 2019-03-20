---
title: Práticas recomendadas do desenvolvedor – segurança de Pod em serviços do Azure Kubernetes (AKS)
description: Conheça as práticas recomendadas de programador para saber como proteger os pods no Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: c2c8522e796fa43f08bf8ad2d61e70a8205d77b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183531"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para segurança de pod no Azure Kubernetes Service (AKS)

À medida que desenvolve e executar aplicações no Azure Kubernetes Service (AKS), a segurança dos seus pods é uma consideração fundamental. As aplicações devem ser concebidas para o princípio do menor número de privilégios necessários. Manter os dados privados seguros é prioritários para os clientes. Não quer credenciais, como cadeias de ligação de base de dados, chaves ou segredos e certificados expostos ao mundo externo em que um invasor poderia aproveitar esses segredos para fins maliciosos. Não adicioná-los ao seu código ou inseri-los em suas imagens de contentor. Esta abordagem seria criar um risco de exposição e limitar a capacidade de rodar essas credenciais como as imagens de contentor tem de ser reconstruída.

Este artigo de melhores práticas que se concentra em pods quão seguras no AKS. Saiba como:

> [!div class="checklist"]
> * Utilizar o contexto de segurança de pod para limitar o acesso aos processos e serviços ou Escalamento de privilégios
> * Autenticar com outros recursos do Azure através de identidades de pod gerido
> * Solicitar e obter credenciais a partir de um cofre digital, como o Azure Key Vault

Também pode ler as melhores práticas para [segurança do cluster] [ best-practices-cluster-security] e para [gerenciamento de imagens de contentor][best-practices-container-image-management].

## <a name="secure-pod-access-to-resources"></a>Pod acesso seguro aos recursos

**Melhores diretrizes de práticas** - para executar como um acesso de utilizador ou grupo e limite diferentes para os serviços e processos de nó subjacentes, definir configurações de contexto de segurança de pod. Atribua o menor número de privilégios necessários.

Para as suas aplicações para serem executados corretamente, pods devem ser executado como pelo usuário ou grupo e não como *raiz*. O `securityContext` para um pod ou contentor permite-lhe definir as definições, tais como *runAsUser* ou *fsGroup* assumir as permissões adequadas. Apenas atribuir o utilizador necessário ou permissões de grupo e não utilize o contexto de segurança como um meio para partem do princípio de permissões adicionais. Quando executa como usuário não raiz, contentores não é possível vincular as portas com privilégios em 1024. Neste cenário, os serviços de Kubernetes pode ser utilizados para dissimular o fato de que uma aplicação está em execução numa porta específica.

Também pode definir um contexto de segurança de pod capacidades adicionais ou permissões para aceder a processos e serviços. As seguintes definições de contexto de segurança comuns podem ser definidas:

* **allowPrivilegeEscalation** define se pode assumir o pod *raiz* privilégios. Conceber a sua aplicação para que esta definição está sempre definida como *false*.
* **Capacidades de Linux** permitem o pod processos subjacentes do nó de acesso. Tenha cuidado com atribuir estas capacidades. Atribua o menor número de privilégios necessários. Para obter mais informações, consulte [capacidades de Linux][linux-capabilities].
* **Etiquetas de SELinux** é um módulo de segurança de kernel de Linux que permite-lhe definir políticas de acesso para acesso de serviços, processos e sistema de ficheiros. Novamente, atribua o menor número de privilégios necessários. Para obter mais informações, consulte [SELinux opções no Kubernetes][selinux-labels]

O manifesto YAML de pod de exemplo seguinte define as definições de contexto para definir a segurança:

* Pod é executado como ID de utilizador *1000* e faça parte de ID de grupo *2000*
* Não é possível aumentar os privilégios para utilizar `root`
* Permite recursos de Linux aceder às interfaces de rede e o relógio de em tempo real (hardware) do anfitrião

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
    securityContext:
      runAsUser: 1000
      fsGroup: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

Trabalhar com o operador de cluster para determinar quais configurações de contexto de segurança que precisa. Tente conceber a sua aplicação para minimizar o pod necessita de acesso e permissões adicionais. Existem recursos de segurança adicional para limitar o acesso com AppArmor e seccomp (computação segura) que pode ser implementado por operadores de cluster. Para obter mais informações, consulte [contentor acesso seguro aos recursos][apparmor-seccomp].

## <a name="limit-credential-exposure"></a>Exposição de credenciais de limite

**Melhores diretrizes de práticas** -não definir credenciais no código da aplicação. Utilize identidades geridas para recursos do Azure para que o acesso de pedido de pod a outros recursos. Um cofre digital, como o Azure Key Vault, também deve ser utilizado para armazenar e obter chaves digitais e as credenciais.

Para limitar o risco de credenciais que são expostas no código da aplicação, evite a utilização de credenciais fixas ou partilhadas. As credenciais ou chaves não devem ser incluídas diretamente em seu código. Se estas credenciais são expostas, o aplicativo precisa ser atualizada e implantados novamente. Uma abordagem melhor é dar pods sua própria identidade e a forma de se autenticar ou obter automaticamente as credenciais a partir de um cofre digital.

O seguinte procedimento [AKS associados projetos open source] [ aks-associated-projects] permitem-lhe autenticar automaticamente os pods ou pedido de credenciais e chaves a partir de um cofre digital:

* Gerido identidades para recursos do Azure, e
* Controlador de FlexVol do Cofre de chaves do Azure

Projetos de código aberto associados do AKS não são suportados pelo suporte técnico do Azure. Eles são fornecidos para recolher comentários e bugs de nossa Comunidade. Esses projetos não são recomendados para utilização em produção.

### <a name="use-pod-managed-identities"></a>Identidades de geridos de pod de utilização

Uma identidade gerida para recursos do Azure permite que um pod autenticar-se em qualquer serviço do Azure que suporta-o como o armazenamento, SQL. O pod está atribuído uma identidade do Azure que permita autenticar para o Azure Active Directory e receber um token digital. Este token digital pode ser apresentado a outros serviços do Azure que verificam se o pod está autorizado a aceder ao serviço e executar as ações necessárias. Essa abordagem significa que não são necessários segredos para cadeias de ligação de base de dados, por exemplo. O fluxo de trabalho simplificado para a identidade de pod gerido é mostrado no diagrama seguinte:

![Fluxo de trabalho simplificado para pod geridos identidade no Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Com uma identidade gerida, o código da aplicação não precisa de incluir as credenciais para aceder um serviço, como o armazenamento do Azure. Como cada pod efetua a autenticação com a sua própria identidade, por isso, pode auditar e rever o acesso. Se seu aplicativo se conecta com outros serviços do Azure, utilize identidades geridas para reutilização de credenciais de limite e o risco de exposição.

Para obter mais informações sobre as identidades de pod, consulte [configurar um cluster do AKS para utilizar identidades de pod gerenciado e com as suas aplicações][aad-pod-identity]

### <a name="use-azure-key-vault-with-flexvol"></a>Utilizar o Cofre de chaves do Azure com FlexVol

Identidades de pod gerido funcionam muito bem para autenticação em relação a suporte de serviços do Azure. Para os seus próprios serviços ou aplicações sem identidades geridas para recursos do Azure, ainda autentica com as credenciais ou chaves. Um cofre digital pode ser utilizado para armazenar estas credenciais.

Quando as aplicações precisam de uma credencial, o que se comunicar com o Cofre digital, obter as credenciais mais recentes e, em seguida, ligue para o serviço necessário. O Azure Key Vault pode ser este cofre digital. O fluxo de trabalho simplificado para obter uma credencial do Azure Key Vault com identidades de pod gerido é mostrado no diagrama seguinte:

![Fluxo de trabalho simplificado para obter uma credencial do Cofre de chaves com um pod de identidade gerido](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Com o Key Vault, armazena e girar regularmente os segredos, como credenciais, as chaves de conta de armazenamento ou certificados. Pode integrar o Azure Key Vault com um cluster do AKS com um FlexVolume. O driver FlexVolume permite que o cluster do AKS nativamente obter as credenciais de Cofre de chaves e fornecer com segurança apenas para o pod solicitante. Trabalhar com o operador de cluster para implementar o controlador de FlexVol do Cofre de chave para os nós do AKS. Pode utilizar uma identidade de pod gerido para pedir acesso ao Key Vault e obter as credenciais que precisa por meio do driver FlexVolume.

## <a name="next-steps"></a>Passos Seguintes

Este artigo concentra-se sobre como proteger os seus pods. Para implementar algumas dessas áreas, consulte os artigos seguintes:

* [Utilizar identidades geridas para recursos do Azure com o AKS][aad-pod-identity]
* [Integrar o Cofre de chaves do Azure com o AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo-pod
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources

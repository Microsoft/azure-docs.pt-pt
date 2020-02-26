---
title: Práticas recomendadas do desenvolvedor – segurança de Pod em serviços do Azure Kubernetes (AKS)
description: Conheça as práticas recomendadas de programador para saber como proteger os pods no Azure Kubernetes Service (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: eaeb81d7f93124f1f3dedf9676314b1b786d8571
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595846"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para segurança de pod no Azure Kubernetes Service (AKS)

À medida que desenvolve e executar aplicações no Azure Kubernetes Service (AKS), a segurança dos seus pods é uma consideração fundamental. As suas candidaturas devem ser concebidas para o princípio do menor número de privilégios necessários. Manter os dados privados seguros é prioritários para os clientes. Você não quer credenciais como cordas de ligação de base de dados, chaves ou segredos e certificados expostos ao mundo exterior onde um intruso poderia aproveitar esses segredos para fins maliciosos. Não adicioná-los ao seu código ou inseri-los em suas imagens de contentor. Esta abordagem seria criar um risco de exposição e limitar a capacidade de rodar essas credenciais como as imagens de contentor tem de ser reconstruída.

Este artigo de boas práticas foca-se em como proteger cápsulas em AKS. Saiba como:

> [!div class="checklist"]
> * Utilizar o contexto de segurança de pod para limitar o acesso aos processos e serviços ou Escalamento de privilégios
> * Autenticar com outros recursos do Azure através de identidades de pod gerido
> * Solicitar e obter credenciais a partir de um cofre digital, como o Azure Key Vault

Pode também ler as melhores práticas para [a segurança][best-practices-cluster-security] do cluster e para gestão de imagem em [contentores.][best-practices-container-image-management]

## <a name="secure-pod-access-to-resources"></a>Pod acesso seguro aos recursos

**Orientação de boas práticas** - Para funcionar como um utilizador ou grupo diferente e limitar o acesso aos processos e serviços subjacentes ao nó, defina as definições de contexto de segurança do pod. Atribua o menor número de privilégios necessários.

Para que as suas aplicações sejam executadas corretamente, as cápsulas devem funcionar como um utilizador ou grupo definido e não como *raiz*. O `securityContext` para uma cápsula ou recipiente permite definir definições como *runAsUser* ou *fsGroup* para assumir as permissões apropriadas. Apenas atribuir o utilizador necessário ou permissões de grupo e não utilize o contexto de segurança como um meio para partem do princípio de permissões adicionais. As *configurações runAsUser*, privilege escalation e outras definições de capacidades linux estão disponíveis apenas em nós e casulos Linux.

Quando executa como usuário não raiz, contentores não é possível vincular as portas com privilégios em 1024. Neste cenário, os serviços de Kubernetes pode ser utilizados para dissimular o fato de que uma aplicação está em execução numa porta específica.

Também pode definir um contexto de segurança de pod capacidades adicionais ou permissões para aceder a processos e serviços. As seguintes definições de contexto de segurança comuns podem ser definidas:

* **permitirPrivilegeEscalation** define se o pod pode assumir privilégios *de raiz.* Desenhe as suas aplicações para que esta definição seja sempre definida como *falsa*.
* **As capacidades** do Linux permitem ao pod aceder aos processos subjacentes ao nó. Tenha cuidado com atribuir estas capacidades. Atribua o menor número de privilégios necessários. Para mais informações, consulte [as capacidades do Linux.][linux-capabilities]
* **As etiquetas SELinux** são um módulo de segurança linux kernel que permite definir políticas de acesso para serviços, processos e acesso ao sistema de ficheiros. Novamente, atribua o menor número de privilégios necessários. Para mais informações, consulte [opções SELinux em Kubernetes][selinux-labels]

O manifesto YAML de pod de exemplo seguinte define as definições de contexto para definir a segurança:

* Pod funciona como ID *1000* do utilizador e parte do grupo ID *2000*
* Não se pode escalar privilégios para usar `root`
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

Trabalhar com o operador de cluster para determinar quais configurações de contexto de segurança que precisa. Tente conceber a sua aplicação para minimizar o pod necessita de acesso e permissões adicionais. Existem recursos de segurança adicional para limitar o acesso com AppArmor e seccomp (computação segura) que pode ser implementado por operadores de cluster. Para mais informações, consulte [o acesso seguro dos contentores aos recursos.][apparmor-seccomp]

## <a name="limit-credential-exposure"></a>Exposição de credenciais de limite

**Orientação de boas práticas** - Não defina credenciais no seu código de candidatura. Utilize identidades geridas para recursos do Azure para que o acesso de pedido de pod a outros recursos. Um cofre digital, como o Azure Key Vault, também deve ser utilizado para armazenar e obter chaves digitais e as credenciais. As identidades geridas pelo pod destinam-se a ser utilizadas apenas com cápsulas Linux e imagens de contentores.

Para limitar o risco de credenciais que são expostas no código da aplicação, evite a utilização de credenciais fixas ou partilhadas. As credenciais ou chaves não devem ser incluídas diretamente em seu código. Se estas credenciais são expostas, o aplicativo precisa ser atualizada e implantados novamente. Uma abordagem melhor é dar pods sua própria identidade e a forma de se autenticar ou obter automaticamente as credenciais a partir de um cofre digital.

Os [seguintes projetos associados][aks-associated-projects] de código aberto AKS permitem autenticar automaticamente cápsulas ou solicitar credenciais e chaves de um cofre digital:

* Gerido identidades para recursos do Azure, e
* Controlador de FlexVol do Cofre de chaves do Azure

Os projetos associados de código aberto AKS não são apoiados pelo apoio técnico do Azure. São fornecidos para recolher feedback e insetos da nossa comunidade. Estes projetos não são recomendados para uso produtivo.

### <a name="use-pod-managed-identities"></a>Identidades de geridos de pod de utilização

Uma identidade gerida para os recursos Azure permite que um pod se autentique contra os serviços Azure que o suportam, como o Storage ou o SQL. O pod está atribuído uma identidade do Azure que permita autenticar para o Azure Active Directory e receber um token digital. Este token digital pode ser apresentado a outros serviços do Azure que verificam se o pod está autorizado a aceder ao serviço e executar as ações necessárias. Essa abordagem significa que não são necessários segredos para cadeias de ligação de base de dados, por exemplo. O fluxo de trabalho simplificado para a identidade de pod gerido é mostrado no diagrama seguinte:

![Fluxo de trabalho simplificado para pod geridos identidade no Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Com uma identidade gerida, o código da aplicação não precisa de incluir as credenciais para aceder um serviço, como o armazenamento do Azure. Como cada pod efetua a autenticação com a sua própria identidade, por isso, pode auditar e rever o acesso. Se seu aplicativo se conecta com outros serviços do Azure, utilize identidades geridas para reutilização de credenciais de limite e o risco de exposição.

Para obter mais informações sobre identidades de pod, consulte [Configure um cluster AKS para usar identidades geridas][aad-pod-identity] por pod e com as suas aplicações

### <a name="use-azure-key-vault-with-flexvol"></a>Utilizar o Cofre de chaves do Azure com FlexVol

Identidades de pod gerido funcionam muito bem para autenticação em relação a suporte de serviços do Azure. Para os seus próprios serviços ou aplicações sem identidades geridas para recursos do Azure, ainda autentica com as credenciais ou chaves. Um cofre digital pode ser utilizado para armazenar estas credenciais.

Quando as aplicações precisam de uma credencial, o que se comunicar com o Cofre digital, obter as credenciais mais recentes e, em seguida, ligue para o serviço necessário. O Azure Key Vault pode ser este cofre digital. O fluxo de trabalho simplificado para obter uma credencial do Azure Key Vault com identidades de pod gerido é mostrado no diagrama seguinte:

![Fluxo de trabalho simplificado para obter uma credencial do Cofre de chaves com um pod de identidade gerido](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Com o Key Vault, armazena e girar regularmente os segredos, como credenciais, as chaves de conta de armazenamento ou certificados. Pode integrar o Azure Key Vault com um cluster do AKS com um FlexVolume. O driver FlexVolume permite que o cluster do AKS nativamente obter as credenciais de Cofre de chaves e fornecer com segurança apenas para o pod solicitante. Trabalhar com o operador de cluster para implementar o controlador de FlexVol do Cofre de chave para os nós do AKS. Pode utilizar uma identidade de pod gerido para pedir acesso ao Key Vault e obter as credenciais que precisa por meio do driver FlexVolume.

O Azure Key Vault com flexVol destina-se a ser utilizado com aplicações e serviços em execução em cápsulas e nós linux.

## <a name="next-steps"></a>Passos seguintes

Este artigo concentra-se sobre como proteger os seus pods. Para implementar algumas dessas áreas, consulte os artigos seguintes:

* [Utilize identidades geridas para recursos Azure com AKS][aad-pod-identity]
* [Integrar o cofre chave azure com AKS][aks-keyvault-flexvol]

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

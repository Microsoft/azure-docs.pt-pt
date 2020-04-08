---
title: Boas práticas de segurança da cápsula
titleSuffix: Azure Kubernetes Service
description: Aprenda as melhores práticas do desenvolvedor para como proteger cápsulas no Serviço Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: zarhoads
ms.openlocfilehash: 1f093b5276ee7ab334043e57f97a108267c32c87
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804389"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>Boas práticas para a segurança do casulo no Serviço Azure Kubernetes (AKS)

À medida que desenvolve e executa aplicações no Serviço Azure Kubernetes (AKS), a segurança das suas cápsulas é uma consideração fundamental. As suas candidaturas devem ser concebidas para o princípio do menor número de privilégios necessários. Manter dados privados seguros é o topo da mente para os clientes. Você não quer credenciais como cordas de ligação de base de dados, chaves ou segredos e certificados expostos ao mundo exterior onde um intruso poderia aproveitar esses segredos para fins maliciosos. Não as adicione ao seu código nem as insera nas imagens do seu recipiente. Esta abordagem criaria um risco de exposição e limitaria a capacidade de rotação dessas credenciais, uma vez que as imagens do contentor terão de ser reconstruídas.

Este artigo de boas práticas foca-se em como proteger cápsulas em AKS. Saiba como:

> [!div class="checklist"]
> * Utilize o contexto de segurança do casulo para limitar o acesso a processos e serviços ou escalada de privilégios
> * Autenticar com outros recursos Azure usando identidades geridas pod
> * Solicitar e recuperar credenciais de um cofre digital como o Cofre chave Azure

Pode também ler as melhores práticas para [a segurança][best-practices-cluster-security] do cluster e para gestão de imagem em [contentores.][best-practices-container-image-management]

## <a name="secure-pod-access-to-resources"></a>Acesso seguro a recursos

**Orientação de boas práticas** - Para funcionar como um utilizador ou grupo diferente e limitar o acesso aos processos e serviços subjacentes ao nó, defina as definições de contexto de segurança do pod. Atribuir o menor número de privilégios necessários.

Para que as suas aplicações sejam executadas corretamente, as cápsulas devem funcionar como um utilizador ou grupo definido e não como *raiz*. O `securityContext` para um vagem ou recipiente permite definir definições como *runAsUser* ou *fsGroup* para assumir as permissões apropriadas. Apenas atribua as permissões de utilizador ou grupo necessárias e não utilize o contexto de segurança como forma de assumir permissões adicionais. As *configurações runAsUser*, privilege escalation e outras definições de capacidades linux estão disponíveis apenas em nós e casulos Linux.

Quando se corre como utilizador não enraizado, os recipientes não podem ligar-se às portas privilegiadas com menos de 1024. Neste cenário, a Kubernetes Services pode ser usada para disfarçar o facto de que uma aplicação está a funcionar numa determinada porta.

Um contexto de segurança do pod também pode definir capacidades ou permissões adicionais para aceder a processos e serviços. Podem ser definidas as seguintes definições de contexto de segurança comum:

* **permitirPrivilegeEscalation** define se o pod pode assumir privilégios *de raiz.* Desenhe as suas aplicações para que esta definição seja sempre definida como *falsa*.
* **As capacidades** do Linux permitem ao pod aceder aos processos subjacentes ao nó. Cuidado com a atribuição destas capacidades. Atribuir o menor número de privilégios necessários. Para mais informações, consulte [as capacidades do Linux.][linux-capabilities]
* **As etiquetas SELinux** são um módulo de segurança linux kernel que permite definir políticas de acesso para serviços, processos e acesso ao sistema de ficheiros. Mais uma vez, atribuir o menor número de privilégios necessários. Para mais informações, consulte [opções SELinux em Kubernetes][selinux-labels]

O manifesto YAML de exemplo seguinte define definições de contexto de segurança para definir:

* Pod funciona como ID *1000* do utilizador e parte do grupo ID *2000*
* Não se pode escalar privilégios para usar`root`
* Permite que as capacidades do Linux acedam às interfaces de rede e ao relógio em tempo real (hardware) do anfitrião

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

Trabalhe com o seu operador de cluster para determinar que definições de contexto de segurança necessita. Tente desenhar as suas aplicações para minimizar permissões adicionais e aceder à cápsula necessária. Existem funcionalidades de segurança adicionais para limitar o acesso utilizando appArmor e seccomp (computação segura) que podem ser implementados pelos operadores de cluster. Para mais informações, consulte [o acesso seguro dos contentores aos recursos.][apparmor-seccomp]

## <a name="limit-credential-exposure"></a>Limitar a exposição à credencial

**Orientação de boas práticas** - Não defina credenciais no seu código de candidatura. Utilize identidades geridas para os recursos do Azure para permitir que o seu casulo solicite acesso a outros recursos. Um cofre digital, como o Azure Key Vault, também deve ser usado para armazenar e recuperar chaves e credenciais digitais. As identidades geridas pelo pod destinam-se a ser utilizadas apenas com cápsulas Linux e imagens de contentores.

Para limitar o risco de as credenciais serem expostas no seu código de aplicação, evite a utilização de credenciais fixas ou partilhadas. Credenciais ou chaves não devem ser incluídas diretamente no seu código. Se estas credenciais forem expostas, a aplicação tem de ser atualizada e reimplantada. Uma melhor abordagem é dar aos pods a sua própria identidade e forma de se autenticarem, ou de recuperarautomaticamente credenciais de um cofre digital.

Os [seguintes projetos associados][aks-associated-projects] de código aberto AKS permitem autenticar automaticamente cápsulas ou solicitar credenciais e chaves de um cofre digital:

* Identidades geridas para recursos Azure, e
* Condutor flexVol do cofre de chaves azure

Os projetos associados de código aberto AKS não são apoiados pelo apoio técnico do Azure. São fornecidos para recolher feedback e insetos da nossa comunidade. Estes projetos não são recomendados para uso produtivo.

### <a name="use-pod-managed-identities"></a>Use identidades geridas por pod

Uma identidade gerida para os recursos Azure permite que um pod se autentique contra os serviços Azure que o suportam, como o Storage ou o SQL. A cápsula é atribuída uma Identidade Azure que lhes permite autenticar o Diretório Ativo Azure e receber um símbolo digital. Este token digital pode ser apresentado a outros serviços Azure que verificam se o casulo está autorizado a aceder ao serviço e executar as ações necessárias. Esta abordagem significa que não são necessários segredos para as cordas de ligação da base de dados, por exemplo. O fluxo de trabalho simplificado para a identidade gerida pela cápsula é mostrado no seguinte diagrama:

![Fluxo de trabalho simplificado para a identidade gerida em Casulo em Azure](media/developer-best-practices-pod-security/basic-pod-identity.png)

Com uma identidade gerida, o seu código de aplicação não precisa de incluir credenciais para aceder a um serviço, como o Azure Storage. Como cada pod autentica com a sua própria identidade, para que possa auditar e rever o acesso. Se a sua aplicação se ligar a outros serviços Azure, utilize identidades geridas para limitar a reutilização da credencial e o risco de exposição.

Para obter mais informações sobre identidades de pod, consulte [Configure um cluster AKS para usar identidades geridas][aad-pod-identity] por pod e com as suas aplicações

### <a name="use-azure-key-vault-with-flexvol"></a>Use cofre de chave azure com FlexVol

As identidades geridas funcionam muito bem para autenticar os serviços de apoio ao Azure. Para os seus próprios serviços ou aplicações sem identidades geridas para recursos Azure, ainda autentica usando credenciais ou chaves. Um cofre digital pode ser usado para armazenar estas credenciais.

Quando as aplicações precisam de uma credencial, comunicam com o cofre digital, recuperam as credenciais mais recentes e, em seguida, ligam-se ao serviço necessário. O Cofre chave azure pode ser este cofre digital. O fluxo de trabalho simplificado para recuperar uma credencial do Cofre chave Azure utilizando identidades geridas por pod é mostrado no seguinte diagrama:

![Fluxo de trabalho simplificado para recuperar uma credencial do Key Vault usando uma identidade gerida por pod](media/developer-best-practices-pod-security/basic-key-vault-flexvol.png)

Com o Key Vault, armazena e gira regularmente segredos como credenciais, chaves de conta de armazenamento ou certificados. Pode integrar o Cofre de Chaves Azure com um cluster AKS utilizando um FlexVolume. O condutor flexVolume permite que o cluster AKS recupere as credenciais do Key Vault e as forneça de forma segura apenas à cápsula de solicitação. Trabalhe com o seu operador de cluster para colocar o condutor FlexVol do Cofre chave nos nós AKS. Pode utilizar uma identidade gerida por um pod para solicitar acesso ao Key Vault e recuperar as credenciais de que necessita através do controlador FlexVolume.

O Azure Key Vault com flexVol destina-se a ser utilizado com aplicações e serviços em execução em cápsulas e nós linux.

## <a name="next-steps"></a>Passos seguintes

Este artigo focou-se em como proteger as suas cápsulas. Para implementar algumas destas áreas, consulte os seguintes artigos:

* [Utilize identidades geridas para recursos Azure com AKS][aad-pod-identity]
* [Integrar o cofre chave azure com AKS][aks-keyvault-flexvol]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-flexvol]: https://github.com/Azure/kubernetes-keyvault-flexvol
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.12/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources

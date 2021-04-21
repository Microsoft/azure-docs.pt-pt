---
title: Versões do Kubernetes suportadas no Azure Kubernetes Service
description: Compreenda a política de suporte da versão Kubernetes e o ciclo de vida dos clusters no Serviço Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 43b0304ec6ac4266c3727990bf013c3dcfe523a3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750253"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Supported Kubernetes versions in Azure Kubernetes Service (AKS) (Versões do Kubernetes suportadas no Azure Kubernetes Service [AKS])

A comunidade kubernetes lança versões menores aproximadamente a cada três meses. Recentemente, a comunidade Kubernetes [aumentou a janela de suporte para cada versão de 9 meses para 12 meses](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), começando com a versão 1.19. 

As versões menores incluem novas funcionalidades e melhorias. As versões de patch são mais frequentes (por vezes semanais) e destinam-se a correções críticas de erros dentro de uma versão menor. As versões de patch incluem correções para vulnerabilidades de segurança ou bugs importantes.

## <a name="kubernetes-versions"></a>Versões Kubernetes

Kubernetes utiliza o esquema padrão [de versão semântica](https://semver.org/) para cada versão:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Cada número na versão indica compatibilidade geral com a versão anterior:

* **As versões principais** mudam quando atualizações incompatíveis da API ou compatibilidade inversa podem ser quebradas.
* **As versões menores** mudam quando são feitas atualizações de funcionalidades que são compatíveis com as outras versões menores.
* **As versões de** correção mudam quando são feitas correções de erro compatíveis com retro-volta.

O objetivo é executar o mais recente patch da versão menor que está a executar. Por exemplo, o seu cluster de produção está **`1.17.7`** ligado. **`1.17.8`** é a mais recente versão de patch disponível para a série *1.17.* Deve atualizar para **`1.17.8`** o mais rapidamente possível para garantir que o seu cluster está totalmente remendado e suportado.

## <a name="kubernetes-version-support-policy"></a>Política de suporte à versão Kubernetes

A AKS define uma versão geralmente disponível como uma versão ativada em todas as medições SLO ou SLA e disponível em todas as regiões. A AKS suporta três versões menores de Ga de Kubernetes:

* A versão mais recente da GA menor que é lançada em AKS (a que nos referimos como N).
* Duas versões menores anteriores.
    * Cada versão menor suportada também suporta um máximo de duas (2) remendos estáveis.

A AKS também pode suportar versões de pré-visualização, que estão explicitamente rotuladas e sujeitas a [termos e condições de pré-visualização][preview-terms].

> [!NOTE]
> A AKS utiliza práticas de implantação seguras que envolvem a implantação gradual da região. Isto significa que pode levar até 10 dias úteis para um novo lançamento ou uma nova versão estar disponível em todas as regiões.

A janela suportada das versões Kubernetes na AKS é conhecida como "N-2": (N (Versão mais recente) - 2 (versões menores)).

Por exemplo, se a AKS introduzir *hoje 1.17.a,* é fornecido suporte para as seguintes versões:

Nova versão menor    |    Lista de versão suportada
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Onde ".letter" é representativo das versões de patch.

Quando uma nova versão menor é introduzida, a versão menor mais antiga e os lançamentos de patch suportados são depreciados e removidos. Por exemplo, a lista de versão suportada atual é:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS lança 1.18. \* , removendo todas as versões 1.15. \* fora do suporte em 30 dias.

> [!NOTE]
> Se os clientes estiverem a executar uma versão Kubernetes não suportada, serão solicitados a fazer upgrade quando solicitarem suporte para o cluster. Os clusters que executam lançamentos kubernetes não suportados não são abrangidos pelas políticas de apoio da [AKS.](./support-policies.md)

Além do acima, a AKS suporta um máximo de dois lançamentos de **patch** de uma determinada versão menor. Assim, dadas as seguintes versões apoiadas:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Se a AKS lançar `1.17.9` `1.16.11` e, as versões patch mais antigas forem depreciadas e removidas, e a lista de versão suportada torna-se:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>`kubectl`Versões suportadas

Pode utilizar uma versão menor mais antiga ou mais recente em `kubectl` relação à sua versão *kube-apiserver,* consistente com a política de [suporte kubernetes para kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Por exemplo, se o seu *kube-apiserver* estiver a *1.17*, então pode utilizar as versões *1.16* a *1.18* de `kubectl` com aquele *kube-apiserver*.

Para instalar ou atualizar a sua versão de `kubectl` , executar `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Processo de libertação e depreciação

Pode fazer referência a lançamentos e depreciações da próxima versão no [calendário de lançamentos AKS Kubernetes](#aks-kubernetes-release-calendar).

Para novas **versões menores** de Kubernetes:
  * A AKS publica um pré-anúncio com a data prevista de um lançamento de nova versão e respetiva depreciação da versão antiga nas notas de lançamento da [AKS](https://aka.ms/aks/releasenotes) pelo menos 30 dias antes da remoção.
  * A AKS usa [o Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) para alertar os utilizadores se uma nova versão causar problemas no seu cluster devido às APIs precídas. O Azure Advisor também é utilizado para alertar o utilizador se atualmente estiverem sem suporte.
  * A AKS publica uma [notificação de saúde](../service-health/service-health-overview.md) de serviço disponível para todos os utilizadores com acesso a AKS e portal, e envia um e-mail aos administradores de subscrição com as datas de remoção da versão planeadas.

    > [!NOTE]
    > Para saber quem são os seus administradores de subscrição ou para alterá-lo, consulte para [gerir as subscrições do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator).
    
  * Os **utilizadores têm 30 dias** desde a remoção da versão para atualizar para uma versão menor suportada para continuar a receber suporte.

Para novas **versões** de patch de Kubernetes:
  * Devido à urgência das versões patch, podem ser introduzidas no serviço à medida que ficam disponíveis.
  * Em geral, a AKS não comunica amplamente o lançamento de novas versões de patch. No entanto, a AKS monitoriza e valida constantemente os patches CVE disponíveis para os suportar em tempo útil. Se for encontrado um patch crítico ou se for necessária uma ação do utilizador, a AKS notificará os utilizadores para fazerem o upgrade para o patch recentemente disponível.
  * Os **utilizadores têm 30 dias** a partir da remoção de um patch da AKS para atualizar para um patch suportado e continuar a receber suporte.

### <a name="supported-versions-policy-exceptions"></a>Exceções políticas de versões apoiadas

A AKS reserva-se o direito de adicionar ou remover versões novas/existentes com um ou mais problemas críticos de impacto de produção ou problemas de segurança sem aviso prévio.

As versões específicas do patch podem ser ignoradas ou aceleradas, dependendo da gravidade do bug ou problema de segurança.

## <a name="azure-portal-and-cli-versions"></a>Versões do portal Azure e CLI

Quando implementa um cluster AKS no portal ou com o CLI Azure, o cluster desrespeibra-se na versão menor N-1 e no patch mais recente. Por exemplo, se a AKS suportar *1.17.a*, *1.17.b*, *1.16.c*, *1.16.d*, *1.15.e*, e *1.15.f,* a versão padrão selecionada é *1.16.c*.

Para saber quais as versões que estão atualmente disponíveis para a sua subscrição e região, utilize o comando [az aks get-verss.][az-aks-get-versions] O exemplo a seguir lista as versões Kubernetes disponíveis para a região *eastus:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>Calendário de lançamento de AKS Kubernetes

Para a história do lançamento passado, consulte [Kubernetes.](https://en.wikipedia.org/wiki/Kubernetes#History)

|  Versão K8s | Libertação a montante  | Pré-visualização da AKS  | AKS GA  | Fim da vida |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | Mar-23-20  | Maio de 2020   | Agosto 2020  | 1.21 GA | 
| 1.19  | Ago-04-20  | Sep 2020   | Nov 2020  | 1.22 GA | 
| 1.20  | Dez-08-20  | jan 2021   | Mar 2021  | 1.23 GA |
| 1.21  | Abr-08-21 | Maio de 2021   | Jun 2021  | 1.24 GA |



## <a name="faq"></a>FAQ

**Como é que a Microsoft me notifica das novas versões Kubernetes?**

A equipa da AKS publica pré-anúncios com datas planeadas das novas versões Kubernetes na nossa documentação, o nosso [GitHub,](https://github.com/Azure/AKS/releases) bem como e-mails para administradores de subscrição que possuem clusters que vão ficar sem suporte.  Além dos anúncios, a AKS também utiliza [o Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) para notificar o cliente dentro do Portal Azure para alertar os utilizadores se estiverem fora de suporte, bem como alertá-los de APIs precítadas que afetarão a sua aplicação ou processo de desenvolvimento. 

**Quantas vezes devo esperar atualizar as versões de Kubernetes para me manter no suporte?**

Começando com Kubernetes 1.19, a comunidade open source expandiu o [apoio para 1 ano](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). A AKS compromete-se a permitir patches e suporte que correspondam aos compromissos a montante. Para os clusters AKS em 1.19 ou maior, você será capaz de atualizar no mínimo uma vez por ano para permanecer numa versão suportada. 

Para versões em 1.18 ou abaixo, a janela de suporte permanece em 9 meses, exigindo uma atualização uma vez a cada 9 meses para permanecer numa versão suportada. Teste regularmente novas versões e esteja preparado para fazer upgrade para versões mais recentes para capturar as mais recentes melhorias estáveis dentro de Kubernetes.

**O que acontece quando um utilizador atualiza um cluster Kubernetes com uma versão menor que não é suportada?**

Se estiver na versão *n-3* ou mais antiga, significa que está fora do suporte e será solicitado para fazer upgrade. Quando a sua atualização da versão n-3 para n-2 tiver sucesso, está de volta às nossas políticas de apoio. Por exemplo:

- Se a versão AKS suportada mais antiga for *1.15.a* e estiver em *1.14.b* ou mais, está fora do suporte.
- Quando você atualizar com sucesso de *1,14.b* para *1.15.a* ou superior, você está de volta dentro das nossas políticas de apoio.

As desvalorizações não são apoiadas.

**O que significa "Fora do Apoio"**

«Fora do apoio», o seguinte:
* A versão que está a executar está fora da lista de versões suportadas.
* Ser-lhe-á pedido que atualize o cluster para uma versão suportada ao solicitar suporte, a menos que esteja dentro do período de graça de 30 dias após a depreciação da versão. 

Além disso, a AKS não faz qualquer tempo de execução ou outras garantias para clusters fora da lista de versões suportadas.

**O que acontece quando um utilizador escala um cluster Kubernetes com uma versão menor que não é suportada?**

Para versões menores não suportadas por AKS, a escala dentro ou fora deve continuar a funcionar. Uma vez que não existem garantias de Qualidade de Serviço, recomendamos a atualização para trazer o seu cluster de volta ao suporte.

**Um utilizador pode ficar numa versão Kubernetes para sempre?**

Se um cluster estiver sem suporte para mais de três (3) versões menores e tiver sido encontrado para transportar riscos de segurança, a Azure contacta-o proactivamente para atualizar o seu cluster. Se não tomar mais medidas, a Azure reserva-se o direito de atualizar automaticamente o seu cluster em seu nome.

**Que versão suporta o plano de controlo se o conjunto de nós não estiver numa das versões AKS suportadas?**

O plano de controlo deve estar dentro de uma janela de versões de todas as piscinas de nós. Para obter informações sobre a atualização do plano de controlo ou piscinas de nó, visite documentação sobre [a atualização das piscinas de nó.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

**Posso saltar várias versões AKS durante a atualização do cluster?**

Quando atualiza um cluster AKS suportado, as versões menores de Kubernetes não podem ser ignoradas. Por exemplo, atualizações entre:
  * *1.12.x*  ->  *1.13.x:* permitido.
  * *1.13.x*  ->  *1.14.x:* permitido.
  * *1.12.x*  ->  *1.14.x:* não é permitido.

Para atualizar a partir de *1.12.x*  ->  *1.14.x*:
1. Upgrade a partir de *1.12.x*  ->  *1.13.x*.
1. Upgrade a partir de *1.13.x*  ->  *1.14.x*.

Saltar várias versões só pode ser feito quando o upgrade de uma versão não suportada de volta para uma versão suportada. Por exemplo, pode fazer upgrade de um *1.10.x* não suportado para um *1.15.x* suportado .

**Posso criar um novo cluster de 1.xx.x durante a sua janela de apoio de 30 dias?**

N.º Uma vez que uma versão é depreciada/removida, não é possível criar um cluster com essa versão. À medida que a mudança for sendo lançado, começarás a ver a versão antiga removida da tua lista de versão. Este processo pode demorar até duas semanas a partir do anúncio, progressivamente por região.

**Estou numa versão recentemente depretada, ainda posso adicionar novas piscinas de nó? Ou terei que fazer upgrade?**

N.º Não lhe será permitido adicionar piscinas de nó da versão depreciada ao seu cluster. Pode adicionar piscinas de nó de uma nova versão. No entanto, isto pode exigir que atualize o plano de controlo primeiro. 

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atualizar o seu cluster, consulte [upgrade de um cluster Azure Kubernetes Service (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

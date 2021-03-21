---
title: Hotpatch para Windows Server Azure Edition (pré-visualização)
description: Saiba como funciona o Hotpatch para o Windows Server Azure Edition e como o habilitar
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 710e6902be6ebe28caaf40fb446e4ee7cd2bf4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687571"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch para novas máquinas virtuais (Pré-visualização)

Hotpatching é uma nova forma de instalar atualizações em novas máquinas virtuais do Windows Server Azure Edition (VMs) que não requerem um reboot após a instalação. Este artigo cobre informações sobre hotpatch para VMs windows server Azure Edition, que tem os seguintes benefícios:
* Menor impacto da carga de trabalho com menos reboots
* Implementação mais rápida de atualizações à medida que os pacotes são menores, instalam-se mais rapidamente e têm uma orquestração de patch mais fácil com o Azure Update Manager
* Melhor proteção, uma vez que os pacotes de atualização hotpatch são abrangidos por atualizações de segurança do Windows que instalam mais rapidamente sem reiniciar

## <a name="how-hotpatch-works"></a>Como funciona a hotpatch

Hotpatch funciona estabelecendo primeiro uma linha de base com uma atualização cumulativa mais recente do Windows Update. Os hotpatches são periodicamente libertados (por exemplo, na segunda terça-feira do mês) que se baseiam nessa linha de base. Os hotpatches conterão atualizações que não requerem um reboot. Periodicamente (a partir de três em três meses), a linha de base é atualizada com uma nova atualização cumulativa mais recente.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Horário da amostra do hotpatch.":::

Existem dois tipos de linhas de base: **linhas de base planeadas** e **linhas de base não planeadas.**
*  **As linhas de base planeadas** são lançadas numa cadência regular, com lançamentos de hotpatch no meio.  As linhas de base planeadas incluem todas as atualizações numa _atualização cumulativa_ comparável para esse mês e requerem um reboot.
    * O calendário de amostras acima ilustra quatro lançamentos de base planeados num ano civil (cinco no total no diagrama) e oito lançamentos de hotpatch.
* **As linhas de base não planeadas** são lançadas quando uma atualização importante (como uma correção de zero dias) é lançada, e essa atualização específica não pode ser lançada como um Hotpatch.  Quando as linhas de base não planeadas forem lançadas, uma versão de hotpatch será substituída por uma linha de base não planeada nesse mês.  As linhas de base não planeadas também incluem todas as atualizações numa _atualização cumulativa_ comparável para esse mês, e também requerem um reboot.
    * O calendário de amostras acima ilustra duas linhas de base não planeadas que substituiriam os lançamentos de hotpatch por esses meses (o número real de linhas de base não planeadas num ano não é conhecido antecipadamente).

## <a name="regional-availability"></a>Disponibilidade regional
Hotpatch está disponível em todas as regiões globais de Azure em pré-visualização. As regiões do Governo de Azure não são apoiadas na pré-estreia.

## <a name="how-to-get-started"></a>Como começar

> [!NOTE]
> Durante a fase de pré-visualização só é possível iniciar no portal Azure utilizando [este link](https://aka.ms/AzureAutomanageHotPatch).

Para começar a utilizar hotpatch num novo VM, siga estes passos:
1.  Permitir o acesso à pré-visualização
    * É necessária uma pré-visualização de acesso único por subscrição.
    * O acesso à pré-visualização pode ser ativado através de API, PowerShell ou CLI, conforme descrito na secção seguinte.
1.  Criar um VM a partir do portal Azure
    * Durante a pré-visualização, terá de começar a utilizar [este link](https://aka.ms/AzureAutomanageHotPatch).
1.  Fornecer detalhes de VM
    * Certifique-se de que _o Windows Server 2019 Datacenter: Azure Edition_ está selecionado no dropdown de imagem)
    * No passo do separador Gestão, desloque-se até à secção 'Guest OS updates'. Verá a instalação hotpatching definida para on and Patch por defeito para remendos orquestrados pelo Azure.
    * As melhores práticas da VM de auto-mutilação serão ativadas por padrão
1. Crie o seu novo VM

## <a name="enabling-preview-access"></a>Permitindo o acesso à pré-visualização

### <a name="rest-api"></a>API REST

O exemplo a seguir descreve como ativar a pré-visualização da sua subscrição:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilize o ```Register-AzProviderFeature``` cmdlet para ativar a pré-visualização da sua subscrição.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>CLI do Azure

Utilize ```az feature register``` para ativar a pré-visualização da sua subscrição.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

A inscrição da funcionalidade pode demorar até 15 minutos. Para verificar o estado do registo:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Uma vez registada a funcionalidade para a sua subscrição, complete o processo de opt-in propagando a alteração no fornecedor de recursos Compute.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Instalação de remendos

Durante a pré-visualização, o [Patching Automático de Convidados VM](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) é ativado automaticamente para todos os VMs criados com _o Windows Server 2019 Datacenter: Azure Edition_. Com remendos automáticos de hóspedes VM habilitados:
* Patches classificados como Critical ou Security são automaticamente descarregados e aplicados no VM.
* As manchas são aplicadas durante as horas de ponta no fuso horário do VM.
* A orquestração de remendos é gerida pelo Azure e os patches são aplicados seguindo [os princípios de disponibilidade primeiro.](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching)
* A saúde da máquina virtual, tal como determinada através de sinais de saúde da plataforma, é monitorizada para detetar falhas de correção.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Como funciona o patching automático de hóspedes VM?

Quando [o Patching Automático de Hóspedes VM](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) está ativado num VM, os patches de Segurança e Crítica disponíveis são descarregados e aplicados automaticamente. Este processo arranca automaticamente todos os meses quando são lançados novos patches. A avaliação e instalação do patch são automáticas, e o processo inclui o reinício do VM conforme necessário.

Com o Hotpatch ativado no _Datacenter 2019 do Windows Server: VMs Azure Edition,_ a maioria das atualizações mensais de segurança são entregues como hotpatches que não requerem reboots. As últimas Atualizações Cumulativas enviadas em meses de base planeados ou não planeados exigirão reboots de VM. Patches adicionais de Crítica ou Segurança também podem estar disponíveis periodicamente, o que pode exigir reboots VM.

O VM é avaliado automaticamente a cada poucos dias e várias vezes dentro de qualquer período de 30 dias para determinar os patches aplicáveis para esse VM. Esta avaliação automática garante que quaisquer patches em falta sejam descobertos o mais rapidamente possível.

Os patches são instalados no prazo de 30 dias após as versões mensais do patch, seguindo [os princípios de disponibilidade primeiro](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching#availability-first-patching). As correções são instaladas apenas durante as horas de ponta para o VM, dependendo do fuso horário do VM. O VM deve estar a funcionar durante as horas de ponta para que os patches sejam automaticamente instalados. Se um VM for desligado durante uma avaliação periódica, o VM será avaliado e os patches aplicáveis serão instalados automaticamente durante a próxima avaliação periódica quando o VM estiver ligado. A próxima avaliação periódica geralmente acontece dentro de poucos dias.

Atualizações de definição e outros patches não classificados como Critical ou Security não serão instalados através de remendos automáticos de hóspedes VM.

## <a name="understanding-the-patch-status-for-your-vm"></a>Compreender o estado do patch para o seu VM

Para ver o estado do patch para o seu VM, navegue na secção de atualizações do **anfitrião Do Convidado +** para o seu VM no portal Azure. Na secção **de atualizações do Guest OS,** clique em 'Ir a Hotpatch (Preview)' para ver o estado de correção mais recente para o seu VM.

Neste ecrã, verá o estado do Hotpatch para o seu VM. Também pode rever se há algum patches disponíveis para o seu VM que não tenham sido instalados. Conforme descrito na secção 'Patch installation' acima, todas as atualizações de segurança e críticas serão automaticamente instaladas no seu VM utilizando [patching automático de convidados VM](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching) e não são necessárias ações extra. Patches com outras classificações de atualização não são instalados automaticamente. Em vez disso, são visualizados na lista de patches disponíveis no separador 'Atualização conformidade'. Também pode ver o histórico de implementações de atualização no seu VM através do 'Histórico de actualização'. É apresentado o histórico de atualização dos últimos 30 dias, juntamente com os detalhes da instalação do patch.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Gestão de Hotpatch.":::

Com o patching automático de hóspedes VM, o seu VM é periodicamente e automaticamente avaliado para atualizações disponíveis. Estas avaliações periódicas garantem a deteção de patches disponíveis. Pode ver os resultados da avaliação no ecrã de Atualizações acima, incluindo a hora da última avaliação. Também pode optar por desencadear uma avaliação de patch a pedido para o seu VM a qualquer momento utilizando a opção "Avaliar agora" e rever os resultados após a conclusão da avaliação.

Semelhante à avaliação a pedido, também pode instalar patches a pedido para o seu VM utilizando a opção "Instalar atualizações agora". Aqui pode optar por instalar todas as atualizações em classificações específicas de patch. Também pode especificar atualizações para incluir ou excluir, fornecendo uma lista de artigos base de conhecimento individuais. Os patches instalados a pedido não são instalados usando princípios de disponibilidade primeiro e podem exigir mais reboots e tempo de inatividade VM para a instalação de atualização.

## <a name="supported-updates"></a>Atualizações suportadas

O Hotpatch cobre as atualizações do Windows Security e mantém a paridade com o conteúdo das atualizações de segurança emitidas no canal regular de atualização do Windows (não Hotpatch).

Existem algumas considerações importantes para executar um VM edição Azure do Windows Server com Hotpatch ativado. As reinicializações ainda são necessárias para instalar atualizações que não estão incluídas no programa Hotpatch. As reinicializações também são necessárias periodicamente após a instalação de uma nova linha de base. Estes reboots mantêm o VM sincronizado com patches de não segurança incluídos na última atualização cumulativa.
* Os patches que atualmente não estão incluídos no programa Hotpatch incluem atualizações de não segurança lançadas para windows e atualizações não-Windows (como patches .NET).  Este tipo de patches tem de ser instalado durante um mês de base e requer um reboot.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-is-hotpatching"></a>O que é hotpatching?

* Hotpatching é uma nova forma de instalar atualizações num Datacenter Windows Server 2019: Azure Edition VM em Azure que não necessita de um reboot após a instalação. Funciona remendando o código de memória dos processos de execução sem a necessidade de reiniciar o processo.

### <a name="how-does-hotpatching-work"></a>Como funciona o hotpatching?

* O hotpatching funciona estabelecendo uma linha de base com uma atualização cumulativa mais recente do Windows Update, e depois baseia-se nessa linha de base com atualizações que não requerem um reboot para entrar em vigor.  A linha de base é atualizada periodicamente com uma nova atualização cumulativa. A atualização cumulativa inclui todas as atualizações de segurança e qualidade e requer um reboot.

### <a name="why-should-i-use-hotpatch"></a>Por que devo usar hotpatch?

* Quando utilizar o Hotpatch no Centro de Dados 2019 do Windows Server: Azure Edition, o seu VM terá maior disponibilidade (menos reboots) e atualizações mais rápidas (pacotes mais pequenos que são instalados mais rapidamente sem a necessidade de reiniciar processos). Este processo resulta num VM que está sempre atualizado e seguro.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Que tipos de atualizações são abrangidas pelo Hotpatch?

* O Hotpatch cobre atualmente as atualizações de segurança do Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Quando receberei a primeira atualização hotpatch?

* As atualizações do Hotpatch são normalmente lançadas na segunda terça-feira de cada mês. Para mais informações, consulte abaixo.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Como será o horário do Hotpatch?

* O hotpatching funciona estabelecendo uma linha de base com uma atualização cumulativa mais recente do Windows Update, e depois baseia-se nessa linha de base com atualizações hotpatch lançadas mensalmente.  Durante a pré-visualização, as linhas de base serão lançadas a partir de três em três meses. Veja a imagem abaixo para um exemplo de um horário anual de três meses (incluindo linhas de base não planeadas devido a correções de zero dias).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Horário da amostra do hotpatch.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Ainda são necessárias reinicializações para um VM matriculado em Hotpatch?

* Os reboots ainda são necessários para instalar atualizações não incluídas no programa Hotpatch, e são necessárias periodicamente após a instalação de uma linha de base (Atualização Cumulativa mais recente do Windows Update). Este reboot manterá o seu VM sincronizado com todos os patches incluídos na atualização cumulativa. As linhas de base (que requerem um reboot) começarão com uma cadência de três meses e aumentarão ao longo do tempo.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>As minhas aplicações são afetadas quando uma atualização do Hotpatch é instalada?

* Como o Hotpatch corrige o código de memória dos processos de execução sem a necessidade de reiniciar o processo, as suas aplicações não serão afetadas pelo processo de remendos. Note que isto está separado de quaisquer potenciais implicações de desempenho e funcionalidade do próprio patch.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Posso desligar o Hotpatch no meu VM?

* Pode desligar o Hotpatch num VM através do portal Azure.  Desligar o Hotpatch irá desenrolar o VM do Hotpatch, que reverte o VM para o comportamento típico de atualização para o Windows Server.  Assim que desenrolar o Hotpatch num VM, pode reinscrever o VM quando a próxima linha de base hotpatch for lançada.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Posso atualizar o meu sistema operativo do Windows Server?

* O upgrade das versões existentes do Windows Server (ou seja, edições não Azure do Windows Server 2016 ou 2019) não é suportado atualmente. O upgrade para futuras versões do Windows Server Azure Edition será suportado.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Posso utilizar o Hotpatch para cargas de trabalho de produção durante a pré-visualização?

* As pré-visualizações destinam-se apenas a ensaios e não a ser utilizadas em ambientes de produção.

### <a name="will-i-be-charged-during-the-preview"></a>Serei cobrado durante a pré-estreia?

* A licença para Windows Server Azure Edition é gratuita durante a pré-visualização. No entanto, o custo de qualquer infraestrutura subjacente configurada para o seu VM (armazenamento, cálculo, networking, etc.) continuará a ser cobrado à sua subscrição.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Como posso conseguir apoio para a resolução de problemas para o Hotpatching?

* Pode arquivar um [bilhete de caso de suporte técnico.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Para a opção Serviço, procure e selecione **Máquina Virtual que executa o Windows** em Compute. Selecione **Azure Features** para o tipo de problema e **Patching automático de convidados VM** para o subtipo de problema.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a Azure Update Management [aqui](https://docs.microsoft.com/azure/automation/update-management/overview).
* Saiba mais sobre patching automático de hóspedes VM [aqui](https://docs.microsoft.com/azure/virtual-machines/automatic-vm-guest-patching)
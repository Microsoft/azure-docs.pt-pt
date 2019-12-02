---
title: Coleta de dados na central de segurança do Azure | Microsoft Docs
description: Este artigo descreve como instalar um agente de Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados coletados.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: ae645f15672693466ba87f2364c756ed164ce629
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74669174"
---
# <a name="data-collection-in-azure-security-center"></a>Coleta de dados na central de segurança do Azure
A central de segurança coleta dados de suas VMs (máquinas virtuais) do Azure, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure (incluindo locais) para monitorar vulnerabilidades de segurança e ameaças. Os dados são coletados usando o agente de Log Analytics, que lê várias configurações relacionadas à segurança e logs de eventos do computador e copia os dados para o espaço de trabalho para análise. Exemplos desses dados são: tipo e versão do sistema operacional, logs do sistema operacional (logs de eventos do Windows), processos em execução, nome do computador, endereços IP e usuário conectado. O agente de Log Analytics também copia arquivos de despejo de memória para seu espaço de trabalho.

A coleta de dados é necessária para fornecer visibilidade de atualizações ausentes, configurações de segurança do sistema operacional configuradas incorretamente, status do Endpoint Protection e detecções de integridade e ameaças. 

Este artigo descreve como instalar um agente de Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados coletados. Ambas as operações são necessárias para habilitar a coleta de dados. 

> [!NOTE]
> - A coleta de dados só é necessária para recursos de computação (VMs, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure). Você pode se beneficiar da central de segurança do Azure mesmo se não provisionar agentes; no entanto, você terá segurança limitada e os recursos listados acima não têm suporte.  
> - Para obter a lista de plataformas com suporte, consulte [plataformas com suporte na central de segurança do Azure](security-center-os-coverage.md).
> - O armazenamento de dados em Log Analytics, independentemente de você usar um espaço de trabalho novo ou existente, pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## Habilitar o provisionamento automático do agente de Log Analytics<a name="auto-provision-mma"></a>

Para coletar os dados dos computadores, você deve ter o agente de Log Analytics instalado. A instalação do agente pode ser feita automaticamente (recomendado) ou você pode instalar o agente manualmente.  

>[!NOTE]
> O provisionamento automático está desativado por padrão. Para definir a central de segurança para instalar o provisionamento automático por padrão, defina-o como **ativado**.
>

Quando o provisionamento automático está ativado, a central de segurança provisiona o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas que forem criadas. O provisionamento automático é altamente recomendado, mas a instalação manual de agentes também está disponível. [Saiba como instalar a extensão do agente de log Analytics](#manual-agent).



Para habilitar o provisionamento automático do agente de Log Analytics:
1. No menu principal da central de segurança, selecione **preços & configurações**.
2. Clique na assinatura aplicável

   ![Selecionar subscrição][7]

3. Selecione a **coleta de dados**.
4. Em **provisionamento automático**, selecione **ativado** para habilitar o provisionamento automático.
5. Selecione **Guardar**.

   ![Ativar o aprovisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como provisionar uma instalação pré-existente, consulte [provisionamento automático em casos de uma instalação de agente preexistente](#preexisting).
> - Para obter instruções sobre o provisionamento manual, consulte [instalar manualmente a extensão do agente de log Analytics](#manual-agent).
> - Para obter instruções sobre como desativar o provisionamento automático, consulte [desligar o provisionamento automático](#offprovisioning).
> - Para obter instruções sobre como carregar a central de segurança usando o PowerShell, consulte [automatizar a integração da central de segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuração do espaço de trabalho
Os dados recolhidos pelo Centro de Segurança são armazenados nas áreas de trabalho do Log Analytics. Você pode optar por ter dados coletados de VMs do Azure armazenadas em espaços de trabalho criados pela central de segurança ou em um espaço de trabalho existente que você criou. 

A configuração do espaço de trabalho é definida por assinatura e muitas assinaturas podem usar o mesmo espaço de trabalho.

### <a name="using-a-workspace-created-by-security-center"></a>Usando um espaço de trabalho criado pela central de segurança

A central de segurança pode criar automaticamente um espaço de trabalho padrão no qual armazenar os dados. 

Para selecionar um espaço de trabalho criado pela central de segurança:

1. Em **configuração padrão do espaço de trabalho**, selecione usar espaços de trabalho criados pela central de segurança.
   ![selecionar o tipo de preço][10] 

1. Clique em **Guardar**.<br>
    A central de segurança cria um novo grupo de recursos e o espaço de trabalho padrão nessa localização geográfica e conecta o agente a esse espaço de trabalho. A Convenção de nomenclatura para o espaço de trabalho e o grupo de recursos é:<br>
   **Espaço de trabalho: defaultworkspace-[ID-da-assinatura]-[geo]<br> grupo de recursos: defaultresource Group-[geográfico]**

   Se uma assinatura contiver VMs de várias geolocalidades, a central de segurança criará vários espaços de trabalho. Vários espaços de trabalho são criados para manter as regras de privacidade de dados.
1. A central de segurança habilitará automaticamente uma solução da central de segurança no espaço de trabalho de acordo com o tipo de preço definido para a assinatura. 

> [!NOTE]
> O tipo de preço Log Analytics dos espaços de trabalho criados pela central de segurança não afeta a cobrança da central de segurança. A faturação do Centro de Segurança baseia-se sempre na sua política de segurança do Centro de Segurança e nas soluções instaladas numa área de trabalho. Para o escalão Gratuito, o Centro de Segurança ativa a solução *SecurityCenterFree* na área de trabalho predefinida. Para o escalão Standard, o Centro de Segurança ativa a solução *Security* na área de trabalho predefinida.
> O armazenamento de dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre contas existentes do log Analytics, consulte [clientes existentes do log Analytics](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Usando um espaço de trabalho existente

Se você já tiver um espaço de trabalho Log Analytics existente, talvez queira usar o mesmo espaço de trabalho.

Para usar seu espaço de trabalho Log Analytics existente, você deve ter permissões de leitura e gravação no espaço de trabalho.

> [!NOTE]
> As soluções habilitadas no espaço de trabalho existente serão aplicadas às VMs do Azure que estão conectadas a ela. Para soluções pagas, isso pode resultar em encargos adicionais. Para considerações de privacidade de dados, verifique se o espaço de trabalho selecionado está na região geográfica correta.
> O armazenamento de dados no log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para selecionar um espaço de trabalho de Log Analytics existente:

1. Em **configuração padrão do espaço de trabalho**, selecione **usar outro espaço de trabalho**.

   ![Selecionar espaço de trabalho existente][2]

2. No menu suspenso, selecione um espaço de trabalho para armazenar os dados coletados.

   > [!NOTE]
   > No menu suspenso, todos os espaços de trabalho em todas as suas assinaturas estão disponíveis. Consulte [seleção de espaço de trabalho de assinatura cruzada](security-center-enable-data-collection.md#cross-subscription-workspace-selection) para obter mais informações. Você deve ter permissão para acessar o espaço de trabalho.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **salvar**, você será perguntado se deseja reconfigurar as VMs monitoradas que foram previamente conectadas a um espaço de trabalho padrão.

   - Selecione **não** se você quiser que as novas configurações de espaço de trabalho sejam aplicadas somente a novas VMS. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente; VMs recém-descobertas que não têm o agente de Log Analytics instalado.
   - Selecione **Sim** se desejar que as novas configurações de espaço de trabalho sejam aplicadas em todas as VMs. Além disso, todas as VMs conectadas a um espaço de trabalho criado pela central de segurança são reconectadas ao novo espaço de trabalho de destino.

   > [!NOTE]
   > Se você selecionar Sim, não deverá excluir os espaços de trabalho criados pela central de segurança até que todas as VMs tenham sido reconectadas ao novo espaço de trabalho de destino. Essa operação falhará se um espaço de trabalho for excluído muito cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecionar espaço de trabalho existente][3]

5. Selecione o tipo de preço para o espaço de trabalho desejado para o qual você pretende definir o agente de Log Analytics. <br>Para usar um espaço de trabalho existente, defina o tipo de preço para o espaço de trabalho. Isso instalará uma solução da central de segurança no espaço de trabalho, se ainda não houver uma.

    a.  No menu principal da central de segurança, selecione **preços & configurações**.
     
    b.  Selecione o espaço de trabalho desejado no qual você pretende conectar o agente.
        ![selecione espaço de trabalho][7] c. Defina o tipo de preço.
        ![selecionar o tipo de preço][9]
   
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução de **segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de espaço de trabalho de assinatura cruzada
Quando você seleciona um espaço de trabalho no qual armazenar seus dados, todos os espaços de trabalho em todas as suas assinaturas estão disponíveis. A seleção da área de trabalho de subscrições cruzadas permite-lhe recolher dados de máquinas virtuais em execução em diferentes subscrições e armazenar os mesmos na área de trabalho da sua preferência. Esta seleção será útil se estiver a utilizar uma área de trabalho centralizada na sua organização e pretender utilizá-la para a recolha de dados de segurança. Para obter mais informações sobre como gerenciar espaços de trabalho, consulte [gerenciar o acesso ao espaço de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Camada de coleta de dados
A seleção de um escalão de recolha de dados no Centro de Segurança do Azure só vai afetar o armazenamento dos eventos de segurança na área de trabalho do Log Analytics. O agente de Log Analytics ainda coletará e analisará os eventos de segurança necessários para as detecções de ameaças da central de segurança do Azure, independentemente da camada de eventos de segurança que você escolher armazenar em seu espaço de trabalho Log Analytics (se houver). Optar por armazenar os eventos de segurança na sua área de trabalho permitirá a investigação, a pesquisa e a auditoria desses eventos na área de trabalho. 
> [!NOTE]
> O armazenamento de dados no log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Você pode escolher a política de filtragem correta para suas assinaturas e espaços de trabalho de quatro conjuntos de eventos a serem armazenados em seu espaço de trabalho: 

- **Nenhum** – desabilitar o armazenamento de eventos de segurança. Esta é a predefinição.
- **Mínimo** – um conjunto menor de eventos para clientes que desejam minimizar o volume do evento.
- **Comum** – esse é um conjunto de eventos que satisfaz a maioria dos clientes e permite que eles tenham uma trilha de auditoria completa.
- **Todos os eventos** – para clientes que desejam garantir que todos os eventos sejam armazenados.


> [!NOTE]
> Esses conjuntos de eventos de segurança estão disponíveis somente na camada Standard da central de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
Esses conjuntos foram projetados para tratar de cenários típicos. Certifique-se de avaliar qual delas atende às suas necessidades antes de implementá-la.
>
>

Para determinar os eventos que pertencerão aos conjuntos de eventos **comuns** e **mínimos** , trabalhamos com clientes e padrões do setor para saber mais sobre a frequência não filtrada de cada evento e seu uso. Utilizamos as seguintes diretrizes neste processo:

- **Mínimo** -certifique-se de que esse conjunto abrange apenas os eventos que podem indicar uma violação bem-sucedida e eventos importantes que têm um volume muito baixo. Por exemplo, esse conjunto contém logon bem-sucedido e com falha do usuário (IDs de evento 4624, 4625), mas não contém logout, o que é importante para auditoria, mas não é significativo para detecção e tem um volume relativamente alto. A maior parte do volume de dados desse conjunto são os eventos de logon e o evento de criação de processo (ID do evento 4688).
- **Comum** -forneça uma trilha de auditoria de usuário completo neste conjunto. Por exemplo, esse conjunto contém logons de usuário e entradas de usuário (ID de evento 4634). Incluímos ações de auditoria, como alterações no grupo de segurança, operações Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações do setor.

Os eventos que têm um volume muito baixo foram incluídos no conjunto comum, pois a principal motivação para escolher todos os eventos é reduzir o volume e não filtrar eventos específicos.

Aqui está uma análise completa das IDs de evento de segurança e do armário de aplicativo para cada conjunto:

| Camada de dados | Indicadores de eventos coletados |
| --- | --- |
| Muito | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Common | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> - Se você estiver usando o objeto de Política de Grupo (GPO), é recomendável habilitar o evento 4688 de criação de processo de políticas de auditoria e o campo *linha de comando* dentro do evento 4688. Para obter mais informações sobre o evento 4688 de criação de processos, consulte [perguntas frequentes](security-center-faq.md#what-happens-when-data-collection-is-enabled)da central de segurança. Para obter mais informações sobre essas políticas de auditoria, consulte [recomendações de política de auditoria](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Para habilitar a coleta de dados para [controles de aplicativo adaptáveis](security-center-adaptive-application.md), a central de segurança configura uma política do AppLocker local no modo de auditoria para permitir todos os aplicativos. Isso fará com que o AppLocker gere eventos que são coletados e aproveitados pela central de segurança. É importante observar que essa política não será configurada em nenhum computador no qual já exista uma política do AppLocker configurada. 
> - Para coletar a ID de [evento 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)da plataforma de filtragem do Windows, você precisa habilitar a [conexão de plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/set/SubCategory: "conexão da plataforma de filtragem"/success: habilitar)
>

Para escolher a política de filtragem:
1. Na página **coleta de dados** , selecione a política de filtragem em **eventos de segurança**.
2. Selecione **Guardar**.

   ![Escolher política de filtragem][5]

### Provisionamento automático em casos de uma instalação de agente pré-existente<a name="preexisting"></a> 

Os casos de uso a seguir especificam como o provisionamento automático funciona em casos em que já existe um agente ou uma extensão instalada. 

- Log Analytics agente está instalado no computador, mas não como uma extensão (agente direto)<br>
Se o agente de Log Analytics estiver instalado diretamente na VM (não como uma extensão do Azure), a central de segurança instalará a extensão do agente de Log Analytics e poderá atualizar o agente do Log Analytics para a versão mais recente.
O agente instalado continuará a relatar para seus espaços de trabalho já configurados e, além disso, reportará para o espaço de trabalho configurado na central de segurança (há suporte para hospedagem múltipla em computadores Windows).
Se o espaço de trabalho configurado for um espaço de trabalho do usuário (não o espaço de trabalho padrão da central de segurança), você precisará instalar a solução "Security/" securityFree "nela para a central de segurança iniciar o processamento de eventos de VMs e computadores que se reportam a esse espaço de trabalho.<br>
<br>
Para computadores Linux, o agente de hospedagem múltipla ainda não tem suporte-portanto, se uma instalação de agente existente for detectada, o provisionamento automático não ocorrerá e a configuração da máquina não será alterada.
<br>
Para computadores existentes nas assinaturas integradas à central de segurança antes de 2019-03-17, quando um agente existente for detectado, a extensão do agente de Log Analytics não será instalada e o computador não será afetado. Para esses computadores, consulte a recomendação "resolver problemas de integridade do agente de monitoramento em suas máquinas" para resolver os problemas de instalação do agente nesses computadores.

  
- O agente de System Center Operations Manager está instalado no computador<br>
A central de segurança instalará a extensão do agente de Log Analytics lado a lado no Operations Manager existente. O agente de Operations Manager existente continuará a relatar para o servidor de Operations Manager normalmente. Observe que o agente de Operations Manager e o agente de Log Analytics compartilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante esse processo.
Observação: se Operations Manager agente versão 2012 estiver instalado, **não** ative o provisionamento automático no.<br>

- Uma extensão de VM pré-existente está presente<br>
    - Quando o agente de monitoramento é instalado como uma extensão, a configuração de extensão permite relatar apenas um único espaço de trabalho. A central de segurança não substitui as conexões existentes aos espaços de trabalho do usuário. A central de segurança armazenará dados de segurança da VM no espaço de trabalho já conectado, desde que a solução "segurança" ou "securityFree" tenha sido instalada nela. A central de segurança pode atualizar a versão da extensão para a versão mais recente neste processo.  
    - Para ver a qual espaço de trabalho a extensão existente está enviando dados, execute o teste para [validar a conectividade com a central de segurança do Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Como alternativa, você pode abrir Log Analytics espaços de trabalho, selecionar um espaço de trabalho, selecionar a VM e examinar a conexão do agente de Log Analytics. 
    - Se você tiver um ambiente em que o agente de Log Analytics está instalado em estações de trabalho cliente e relatando para um espaço de Log Analytics existente, examine a lista de [sistemas operacionais com suporte pela central de segurança do Azure](security-center-os-coverage.md) para verificar se o sistema operacional tem suporte. Para obter mais informações, consulte [clientes existentes do log Analytics](security-center-faq.md#existingloganalyticscust).
 
### Desligar o provisionamento automático<a name="offprovisioning"></a>
Você pode desativar o provisionamento automático de recursos a qualquer momento, desativando essa configuração na política de segurança. 


1. Retorne ao menu principal da central de segurança e selecione a política de segurança.
2. Clique em **Editar configurações** na linha da assinatura para a qual você deseja desabilitar o provisionamento automático.
3. Na folha **política de segurança – coleta de dados** , em **provisionamento automático** , selecione **desativado**.
4. Selecione **Guardar**.

   ![Desabilitar provisionamento automático][6]

Quando o provisionamento automático está desabilitado (desativado), a seção de configuração do espaço de trabalho padrão não é exibida.

Se você desativar o provisionamento automático após ele ter sido anteriormente:
-   Os agentes não serão provisionados em novas VMs.
-   A central de segurança para de coletar dados do espaço de trabalho padrão.
 
> [!NOTE]
>  Desabilitar o provisionamento automático não remove o agente de Log Analytics das VMs do Azure em que o agente foi provisionado. Para obter informações sobre como remover a extensão do OMS, consulte [como fazer remover extensões do OMS instaladas pela central de segurança](security-center-faq.md#remove-oms).
>
    
## Provisionamento manual de agentes<a name="manual-agent"></a>
 
Há várias maneiras de instalar o agente de Log Analytics manualmente. Ao instalar manualmente, certifique-se de desabilitar o provisionamento automático.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implantação de extensão de VM do Operations Management Suite 

Você pode instalar manualmente o agente de Log Analytics, para que a central de segurança possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas.
1. Selecione provisionamento automático – desativado.
2. Crie um espaço de trabalho e defina o tipo de preço para o espaço de trabalho para o qual você pretende definir o agente de Log Analytics:

   a.  No menu principal da central de segurança, selecione **política de segurança**.
     
   b.  Selecione o espaço de trabalho no qual você pretende conectar o agente. Verifique se o espaço de trabalho está na mesma assinatura usada na central de segurança e se você tem permissões de leitura/gravação no espaço de trabalho.
       ![selecionar espaço de trabalho][8]
3. Defina o tipo de preço.
   ![selecionar o tipo de preço][9] 
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução de **segurança** ou **SecurityCenterFree** habilitada, o preço será definido automaticamente. 
   > 

4. Se você quiser implantar os agentes em novas VMs usando um modelo do Resource Manager, instale a extensão da máquina virtual do OMS:

   a.  [Instalar a extensão da máquina virtual do OMS para Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalar a extensão da máquina virtual do OMS para Linux](../virtual-machines/extensions/oms-linux.md)
5. Para implantar as extensões em VMs existentes, siga as instruções em [coletar dados sobre máquinas virtuais do Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A seção **coletar dados de eventos e de desempenho** é opcional.
   >
6. Para usar o PowerShell para implantar a extensão, use o seguinte exemplo do PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Vá para **log Analytics** e clique em **Configurações avançadas**.
    
      ![Definir log Analytics][11]

   2. Copie os valores fora de **workspaceid** e de **chave primária**.
  
      ![Copiar valores][12]

   3. Preencha a configuração pública e a configuração privada com estes valores:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Ao instalar em uma VM do Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Ao instalar em uma VM Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Para obter instruções sobre como carregar a central de segurança usando o PowerShell, consulte [automatizar a integração da central de segurança do Azure usando o PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Resolução de problemas

-   Para identificar problemas de instalação de provisionamento automático, consulte [monitoramento de problemas de integridade do agente](security-center-troubleshooting-guide.md#mon-agent).

-  Para identificar os requisitos de rede do agente de monitoramento, consulte [Solucionando problemas de requisitos de rede do agente de monitoramento](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de integração manual, consulte [como solucionar problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Para identificar problemas de computadores e VMs não monitorados, consulte [máquinas virtuais e computadores não monitorados](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Passos seguintes
Este artigo mostrou como a coleta de dados e o provisionamento automático na central de segurança funcionam. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png

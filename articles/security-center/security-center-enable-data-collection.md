---
title: Recolha de dados no Centro de segurança do Azure | Documentos da Microsoft
description: " Saiba como ativar a recolha de dados no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2019
ms.author: v-mohabe
ms.openlocfilehash: e87ea5f6d8a92f18fc1b289ebf9ffd1cc0326812
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845911"
---
# <a name="data-collection-in-azure-security-center"></a>Recolha de dados no Centro de segurança do Azure
A central de segurança coleta dados de suas VMs (máquinas virtuais) do Azure, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure (incluindo locais) para monitorar vulnerabilidades de segurança e ameaças. Os dados são coletados usando o agente de Log Analytics, que lê várias configurações relacionadas à segurança e logs de eventos do computador e copia os dados para o espaço de trabalho para análise. Exemplos destes dados são: operação sistema tipo e versão, (registos de eventos Windows), de registos de sistema operativo processos em execução, nome da máquina, endereços IP e com sessão iniciada no utilizador. O agente de Log Analytics também copia arquivos de despejo de memória para seu espaço de trabalho.

Recolha de dados é necessária para fornecer visibilidade em falta atualizações, configurações de segurança de SO configurado incorretamente, ativação de proteção de ponto final e deteções de ameaças e estado de funcionamento. 

Este artigo fornece orientação sobre como instalar um agente de Log Analytics e definir um espaço de trabalho Log Analytics no qual armazenar os dados coletados. As duas operações são necessários para ativar a recolha de dados. 

> [!NOTE]
> - A coleta de dados só é necessária para recursos de computação (VMs, conjuntos de dimensionamento de máquinas virtuais, contêineres de IaaS e computadores não Azure). Pode tirar partido do Centro de segurança do Azure, mesmo se não aprovisionar agentes; No entanto, será tem segurança limitada e os recursos listados acima não são suportados.  
> - Para obter a lista de plataformas suportadas, consulte [plataformas suportadas no Centro de segurança do Azure](security-center-os-coverage.md).
> - O armazenamento de dados em Log Analytics, independentemente de você usar um espaço de trabalho novo ou existente, pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## Habilitar o provisionamento automático do agente de Log Analytics<a name="auto-provision-mma"></a>

Para coletar os dados dos computadores, você deve ter o agente de Log Analytics instalado.  A instalação do agente pode ser feita automaticamente (recomendado) ou você pode instalar o agente manualmente.  

>[!NOTE]
> Aprovisionamento automático está desativada por predefinição. Para definir o Centro de segurança para instalar o aprovisionamento automático por predefinição, defina-o como **no**.
>

Quando o provisionamento automático está ativado, a central de segurança provisiona o agente de Log Analytics em todas as VMs do Azure com suporte e quaisquer novas que forem criadas. Aprovisionamento Automático é vivamente recomendado mas instalação manual de agente também está disponível. [Saiba como instalar a extensão do agente de log Analytics](#manualagent).



Para habilitar o provisionamento automático do agente de Log Analytics:
1. No menu principal da central de segurança, selecione **preços & configurações**.
2. Clique na assinatura aplicável

   ![Selecionar subscrição][7]

3. Selecione a **coleta de dados**.
4. Sob **aprovisionamento automático**, selecione **no** para ativar o aprovisionamento automático.
5. Selecione **Guardar**.

   ![Ativar o aprovisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como aprovisionar uma instalação já existente, consulte [aprovisionamento automático em casos de uma instalação de agente preexistente](#preexisting).
> - Para obter instruções sobre o provisionamento manual, consulte [instalar manualmente a extensão do agente de log Analytics](#manualagent).
> - Para obter instruções sobre como desativar aprovisionamento automático, consulte [desativar aprovisionamento automático](#offprovisioning).
> - Para obter instruções sobre como integrar o Centro de segurança com o PowerShell, consulte [automatizar a inclusão do Centro de segurança do Azure com o PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuração de área de trabalho
Dados recolhidos pelo centro de segurança são armazenados nas áreas de trabalho do Log Analytics.  Pode optar por ter dados recolhidos das VMs do Azure armazenados em áreas de trabalho criadas pelo centro de segurança ou numa área de trabalho existente que criou. 

Configuração de área de trabalho é definida por subscrição, e o número de subscrições, pode utilizar a mesma área de trabalho.

### <a name="using-a-workspace-created-by-security-center"></a>Utilizar uma área de trabalho criada pelo centro de segurança

Centro de segurança pode criar automaticamente uma área de trabalho predefinida para armazenar os dados. 

Para selecionar uma área de trabalho criada pelo centro de segurança:

1. Sob **configuração de área de trabalho predefinida**, selecione utiliza áreas de trabalho criadas pelo centro de segurança.
   ![Selecione o escalão de preço][10] 

1. Clique em **Guardar**.<br>
    Centro de segurança cria uma novo recurso grupo predefinido área de trabalho e nessa localização geográfica e liga o agente a essa área de trabalho. A Convenção de nomenclatura para a área de trabalho e grupo de recursos é:<br>
   **Espaço Defaultworkspace-[ID-da-assinatura]-[<br> geo] grupo de recursos: Defaultresource: [geo]**

   Se uma subscrição contém VMs a partir de localizações geográficas vários, em seguida, o Centro de segurança cria várias áreas de trabalho. Várias áreas de trabalho são criadas para manter as regras da privacidade de dados.
1. Centro de segurança habilitará automaticamente uma solução de centro de segurança na área de trabalho pelo escalão de preço definido para a subscrição. 

> [!NOTE]
> O escalão de áreas de trabalho criadas pelo centro de segurança de preços do Log Analytics não afeta a faturação do Centro de segurança. A faturação do Centro de segurança baseia-se sempre em sua política de segurança do Centro de segurança e as soluções instaladas numa área de trabalho. Para o escalão gratuito, o Centro de segurança permite que o *SecurityCenterFree* solução na área de trabalho predefinida. No escalão Standard, o Centro de segurança permite que o *segurança* solução na área de trabalho predefinida.
> O armazenamento de dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre contas existentes do log Analytics, consulte [clientes existentes do log Analytics](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Utilizar uma área de trabalho existente

Se já tiver uma área de trabalho do Log Analytics existente que poderá pretender utilizar a mesma área de trabalho.

Para utilizar a sua área de trabalho do Log Analytics existente, tem de ter de leitura e escrita permissões na área de trabalho.

> [!NOTE]
> Soluções ativadas na área de trabalho existente serão aplicadas a VMs do Azure que estão ligados ao mesmo. Para as soluções pagas, isto pode resultar em encargos adicionais. Para considerações de privacidade de dados, certificar-se de que sua área de trabalho selecionada está na região geográfica certa.
> O armazenamento de dados no log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para selecionar uma área de trabalho do Log Analytics existente:

1. Sob **configuração de área de trabalho predefinida**, selecione **utilizar outra área de trabalho**.

   ![Selecione a área de trabalho existente][2]

2. No menu pendente, selecione uma área de trabalho para armazenar os dados recolhidos.

   > [!NOTE]
   > O pull menu pendente, todas as áreas de trabalho em todas as suas subscrições estão disponíveis. Ver [cruzado a seleção de área de trabalho de subscrição](security-center-enable-data-collection.md#cross-subscription-workspace-selection) para obter mais informações. Tem de ter permissão para aceder à área de trabalho.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **guardar**, será solicitado se pretende reconfigurar VMs monitorizadas que foram anteriormente ligadas a uma área de trabalho predefinida.

   - Selecione **não** se pretender que as novas definições de área de trabalho para aplicar apenas a novas VMs. As novas configurações de espaço de trabalho se aplicam somente a novas instalações de agente; VMs recém-descobertas que não têm o agente de Log Analytics instalado.
   - Selecione **Sim** se pretender que as novas definições de área de trabalho para aplicar em todas as VMs. Além disso, todas as VMS ligadas a um centro de segurança criada a área de trabalho for reconectada para a nova área de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, não tem de eliminar as áreas de trabalho criadas pelo centro de segurança até que todas as VMs tenham sido reconectadas à nova área de trabalho de destino. Esta operação falha se uma área de trabalho é eliminada demasiado antigo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecione a área de trabalho existente][3]

5. Selecione o tipo de preço para o espaço de trabalho desejado para o qual você pretende definir o agente de Log Analytics. <br>Para utilizar uma área de trabalho existente, defina o escalão de preço para a área de trabalho. Esta ação irá instalar uma solução de centro de segurança na área de trabalho, se já não estiver presente.

    a.  No menu principal da central de segurança, selecione **preços & configurações**.
     
    b.  Selecione o espaço de trabalho desejado no qual você pretende conectar o agente.
        ![Selecione a área de trabalho][7] c. Defina o escalão de preço.
        ![Selecione o escalão de preço][9]
   
   >[!NOTE]
   >Se já tiver uma área de trabalho de um **Security** ou **SecurityCenterFree** solução ativada, os preços serão definidos automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de área de trabalho entre subscrições
Quando seleciona uma área de trabalho para armazenar seus dados, todas as áreas de trabalho em todas as suas subscrições estão disponíveis. Seleção de área de trabalho de subscrição cruzada permite-lhe recolher dados de máquinas virtuais em execução em diferentes subscrições e armazená-la na área de trabalho da sua preferência. Esta seleção é útil se estiver a utilizar uma área de trabalho centralizada na sua organização e pretender utilizá-lo para a recolha de dados de segurança. Para obter mais informações sobre como gerir áreas de trabalho, consulte [gerir o acesso de área de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Camada de recolha de dados
Selecionar um escalão de recolha de dados no Centro de segurança do Azure só afetarão o armazenamento de eventos de segurança na sua área de trabalho do Log Analytics. O agente de Log Analytics ainda coletará e analisará os eventos de segurança necessários para as detecções de ameaças da central de segurança do Azure, independentemente da camada de eventos de segurança que você escolher armazenar em seu espaço de trabalho Log Analytics (se houver). Selecionar a opção para armazenar eventos de segurança na sua área de trabalho irá permitir a investigação, a pesquisa e a auditoria desses eventos na sua área de trabalho. 
> [!NOTE]
> O armazenamento de dados no log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais detalhes, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Pode escolher o direito de política para as suas subscrições e áreas de trabalho do quatro conjuntos de eventos de filtragem para serem armazenados na sua área de trabalho: 

- **Nenhum** – desativar o armazenamento de eventos de segurança. Esta é a predefinição.
- **Mínimo** – um conjunto menor de eventos para os clientes que desejam minimizar o volume de eventos.
- **Comuns** – este é um conjunto de eventos que satisfaça a maioria dos clientes e permite-lhes uma trilha de auditoria completa.
- **Todos os eventos** – para os clientes que pretendem para se certificar de que todos os eventos são armazenados.


> [!NOTE]
> Estes conjuntos de eventos de segurança estão disponíveis apenas no escalão Standard do Centro de segurança. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
Estes conjuntos foram projetados para abordar cenários típicos. Certifique-se avaliar qual atende às suas necessidades antes de a implementar.
>
>

Para determinar os eventos que irão pertencer à **comuns** e **mínima** conjuntos de eventos, trabalhamos com clientes e os padrões da indústria para saber mais sobre a frequência não filtrada cada evento e a utilização das mesmas. Usamos as seguintes diretrizes nesse processo:

- **Mínimo** -Certifique-se de que este conjunto abrange apenas os eventos que podem indicar uma violação com êxito e eventos importantes que têm um volume muito baixo. Por exemplo, esse conjunto contém logon bem-sucedido e com falha do usuário (IDs de evento 4624, 4625), mas não contém logout, o que é importante para auditoria, mas não é significativo para detecção e tem um volume relativamente alto. A maioria do volume de dados deste conjunto é os eventos de início de sessão e o evento de criação de processo (evento ID 4688).
- **Comuns** -fornecer uma trilha de auditoria de utilizador completo neste conjunto. Por exemplo, esse conjunto contém logons de usuário e entradas de usuário (ID de evento 4634). Podemos incluir a auditoria de ações como alterações do grupo de segurança, operações de Kerberos do controlador de domínio de chaves e outros eventos que são recomendados por organizações do setor.

Eventos com um volume muito baixa foram incluídos em comum definir como a principal motivação para escolher ao longo de todos os eventos é reduzir o volume e não para filtrar os eventos específicos.

Aqui está uma divisão completa a segurança e do App Locker de IDs de evento para cada conjunto:

| Camada de dados | Indicadores de eventos recolhidos |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Se estiver a utilizar o objeto de política de grupo (GPO), recomenda-se que ative as políticas de auditoria 4688 de evento do processo de criação e o *CommandLine* campo dentro 4688 de evento. Para obter mais informações sobre a 4688 de eventos de criação de processo, consulte o Centro de segurança [FAQ](security-center-faq.md#what-happens-when-data-collection-is-enabled). Para obter mais informações sobre estas políticas de auditoria, veja [recomendações de políticas de auditoria](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Para ativar a recolha de dados para [controlos de aplicações adaptativos](security-center-adaptive-application.md), Centro de segurança configura uma política de AppLocker local no modo de auditoria para permitir que todos os aplicativos. Isso fará com que o AppLocker gerar eventos que, em seguida, são recolhidos e utilizados pelo centro de segurança. É importante observar que esta política não irá ser configurada em quaisquer máquinas em que já existe uma política de AppLocker configurada. 
> - Para recolher a Windows Filtering Platform [5156 de ID de evento](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), tem de ativar [ligação de plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol/conjuntos /subcategory: /Success:Enable "Ligação de plataforma de filtragem")
>

Para escolher a política de filtragem:
1. Na página **coleta de dados** , selecione a política de filtragem em **eventos de segurança**.
2. Selecione **Guardar**.

   ![Escolha a política de filtragem][5]

### O aprovisionamento automático em casos de uma instalação de agente preexistente <a name="preexisting"></a> 

Os seguintes casos de utilização especificar aprovisionar como automática funciona em casos quando já existe um agente ou a extensão instalada. 

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

- Uma extensão de VM já existente está presente<br>
    - Quando o agente de monitoramento é instalado como uma extensão, a configuração de extensão permite relatar apenas um único espaço de trabalho. Centro de segurança não substitui as ligações existentes a áreas de trabalho do utilizador. A central de segurança armazenará dados de segurança da VM no espaço de trabalho já conectado, desde que a solução "segurança" ou "securityFree" tenha sido instalada nela. A central de segurança pode atualizar a versão da extensão para a versão mais recente neste processo.  
    - Para ver a área de trabalho que a extensão existente está a enviar dados para executar o teste seja [valide a conectividade com o Centro de segurança do Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Como alternativa, você pode abrir Log Analytics espaços de trabalho, selecionar um espaço de trabalho, selecionar a VM e examinar a conexão do agente de Log Analytics. 
    - Se você tiver um ambiente em que o agente de Log Analytics está instalado em estações de trabalho cliente e relatando para um espaço de Log Analytics existente, examine a lista de [sistemas operacionais com suporte pela central de segurança do Azure](security-center-os-coverage.md) para verificar se o sistema operacional está com suporte e consulte [clientes do log Analytics existentes](security-center-faq.md#existingloganalyticscust) para obter mais informações.
 
### Desativar aprovisionamento automático <a name="offprovisioning"></a>
Pode desativar aprovisionamento automático de recursos em qualquer altura ao desativar esta definição na política de segurança. 


1. Regresse ao menu principal do Centro de segurança e selecione a política de segurança.
2. Clique em **Editar configurações** na linha da assinatura para a qual você deseja desabilitar o provisionamento automático.
3. Sobre o **política de segurança – recolha de dados** painel, em **aprovisionamento automático** selecionar **desativar**.
4. Selecione **Guardar**.

   ![Desativar aprovisionamento automático][6]

Quando o aprovisionamento automático está desativado (desativado), a seção de configuração de área de trabalho predefinido não é apresentada.

Se optar por desativar aprovisionamento automático após estava anteriormente em:
-   Agentes não serão aprovisionados em novas VMs.
-   Centro de segurança para a recolha de dados de área de trabalho predefinida.
 
> [!NOTE]
>  Desabilitar o provisionamento automático não remove o agente de Log Analytics das VMs do Azure em que o agente foi provisionado. Para informações sobre como remover a extensão do OMS, consulte [como posso remover extensões OMS instaladas pelo centro de segurança](security-center-faq.md#remove-oms).
>
    
## Manual do agente de aprovisionamento <a name="manualagent"></a>
 
Há várias maneiras de instalar o agente de Log Analytics manualmente. Ao instalar manualmente, certifique-se de que desativa aprovisionamento automático.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementação de extensão de VM de pacote de gestão de operações 

Você pode instalar manualmente o agente de Log Analytics, para que a central de segurança possa coletar dados de segurança de suas VMs e fornecer recomendações e alertas.
1. Selecione o aprovisionamento automático – DESATIVADO.
2. Crie um espaço de trabalho e defina o tipo de preço para o espaço de trabalho para o qual você pretende definir o agente de Log Analytics:

   a.  No menu principal do Centro de segurança, selecione **política de segurança**.
     
   b.  Selecione a área de trabalho no qual pretende ligar o agente. Certificar-se de que a área de trabalho está na mesma subscrição, utilize no Centro de segurança e que tem permissões de leitura/escrita na área de trabalho.
       ![Selecione a área de trabalho][8]
3. Defina o escalão de preço.
   ![Selecione o escalão de preço][9] 
   >[!NOTE]
   >Se já tiver uma área de trabalho de um **Security** ou **SecurityCenterFree** solução ativada, os preços serão definidos automaticamente. 
   > 

4. Se quiser implantar os agentes nos novas VMs utilizando um modelo do Resource Manager, instale a extensão de máquina virtual do OMS:

   a.  [Instalar a extensão de máquina virtual do OMS para Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instalar a extensão de máquina virtual do OMS para Linux](../virtual-machines/extensions/oms-linux.md)
5. Para implementar as extensões em VMs existentes, siga as instruções em [recolher dados sobre máquinas virtuais do Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A secção **recolher dados de eventos e desempenho** é opcional.
   >
6. Para utilizar o PowerShell para implementar a extensão, utilize o seguinte exemplo do PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Aceda a **do Log Analytics** e clique em **definições avançadas**.
    
      ![Conjunto o log analytics][11]

   2. Copie os valores de **WorkspaceID** e **chave primária**.
  
      ![Copiar valores][12]

   3. Preencha a configuração pública e a configuração privada com estes valores:
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Quando instalar numa VM do Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Quando instalar numa VM do Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Para obter instruções sobre como integrar o Centro de segurança com o PowerShell, consulte [automatizar a inclusão do Centro de segurança do Azure com o PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Resolução de problemas

-   Para identificar problemas de instalação de aprovisionamento automático, consulte [problemas de estado de funcionamento do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent).

-  Para identificar os requisitos de rede monitorização do agente, consulte [resolução de problemas requisitos de rede do agente de monitorização](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de integração manual, consulte [como resolver problemas de integração do Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Para identificar problemas de computadores e de VMs não monitorizadas, veja [computadores e VMs não monitorizadas](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Passos Seguintes
Este artigo mostrou como a recolha de dados e o aprovisionamento automático no Centro de segurança funciona. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

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

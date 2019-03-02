---
title: Recolha de dados no Centro de segurança do Azure | Documentos da Microsoft
description: " Saiba como ativar a recolha de dados no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/2/2018
ms.author: rkarlin
ms.openlocfilehash: 09abb8fa2c8d08b27b83a2510b4c1133458b8363
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240733"
---
# <a name="data-collection-in-azure-security-center"></a>Recolha de dados no Centro de segurança do Azure
Centro de segurança recolhe dados a partir das suas máquinas virtuais do Azure (VMs) e computadores não Azure para monitorizar ameaças e vulnerabilidades de segurança. Dados são recolhidos com o agente de Log Analytics, que lê várias configurações relacionadas à segurança e logs de eventos a partir da máquina e copia os dados para a área de trabalho para análise. Exemplos destes dados são: operação sistema tipo e versão, (registos de eventos Windows), de registos de sistema operativo processos em execução, nome da máquina, endereços IP e com sessão iniciada no utilizador. O agente Log Analytics também copia os ficheiros de informação de falha para a área de trabalho.

Recolha de dados é necessária para fornecer visibilidade em falta atualizações, configurações de segurança de SO configurado incorretamente, ativação de proteção de ponto final e deteções de ameaças e estado de funcionamento. 

Este artigo fornece orientações sobre como instalar um agente de Log Analytics e definir uma área de trabalho do Log Analytics para armazenar os dados recolhidos. As duas operações são necessários para ativar a recolha de dados. 

> [!NOTE]
> - Recolha de dados só é necessário para recursos de computação (VMs e computadores não Azure). Pode tirar partido do Centro de segurança do Azure, mesmo se não aprovisionar agentes; No entanto, será tem segurança limitada e os recursos listados acima não são suportados.  
> - Para obter a lista de plataformas suportadas, consulte [plataformas suportadas no Centro de segurança do Azure](security-center-os-coverage.md).
> - Recolha de dados para o conjunto de dimensionamento de Máquina Virtual não é atualmente suportada.


## Ativar o aprovisionamento automático do agente do Log Analytics <a name="auto-provision-mma"></a>

Para recolher os dados das máquinas deve ter instalado o agente de Log Analytics.  Instalação do agente pode ser automaticamente (recomendado) ou pode optar por instalar o agente manualmente.  

>[!NOTE]
> Aprovisionamento automático está desativada por predefinição. Para definir o Centro de segurança para instalar o aprovisionamento automático por predefinição, defina-o como **no**.
>

Quando o aprovisionamento automático está ativado, o Centro de segurança Aprovisiona o agente do Log Analytics em todas as VMs do Azure e novas que são criadas. Aprovisionamento Automático é vivamente recomendado mas instalação manual de agente também está disponível. [Saiba como instalar a extensão de agente do Log Analytics](#manualagent).



Para ativar o aprovisionamento automático do agente do Log Analytics:
1. No menu principal do Centro de segurança, selecione **política de segurança**.
2. Clique em **editar as definições de** na coluna Settings da subscrição pretendida na lista.

  ![Selecionar subscrição][7]

3. Em **Política de segurança**, selecione **Recolha de Dados**.
4. Sob **aprovisionamento automático**, selecione **no** para ativar o aprovisionamento automático.
5. Selecione **Guardar**.

  ![Ativar o aprovisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como aprovisionar uma instalação já existente, consulte [aprovisionamento automático em casos de uma instalação de agente preexistente](#preexisting).
> - Para obter instruções sobre o serviço de aprovisionamento manual, consulte [instalar manualmente a extensão de agente do Log Analytics](#manualagent).
> - Para obter instruções sobre como desativar aprovisionamento automático, consulte [desativar aprovisionamento automático](#offprovisioning).
> - Para obter instruções sobre como integrar o Centro de segurança com o PowerShell, consulte [automatizar a inclusão do Centro de segurança do Azure com o PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuração de área de trabalho
Dados recolhidos pelo centro de segurança são armazenados nas áreas de trabalho do Log Analytics.  Pode optar por ter dados recolhidos das VMs do Azure armazenados em áreas de trabalho criadas pelo centro de segurança ou numa área de trabalho existente que criou. 

Configuração de área de trabalho é definida por subscrição, e o número de subscrições, pode utilizar a mesma área de trabalho.

### <a name="using-a-workspace-created-by-security-center"></a>Utilizar uma área de trabalho criada pelo centro de segurança

Centro de segurança pode criar automaticamente uma área de trabalho predefinida para armazenar os dados. 

Para selecionar uma área de trabalho criada pelo centro de segurança:

1.  Sob **configuração de área de trabalho predefinida**, selecione utiliza áreas de trabalho criadas pelo centro de segurança.
   ![Selecione o escalão de preço][10] 

2. Clique em **Guardar**.<br>
    Centro de segurança cria uma novo recurso grupo predefinido área de trabalho e nessa localização geográfica e liga o agente a essa área de trabalho. A Convenção de nomenclatura para a área de trabalho e grupo de recursos é:<br>
**Área de trabalho: DefaultWorkspace-[subscrição-ID]-[geo]<br> grupo de recursos: DefaultResourceGroup-[geo]**

   Se uma subscrição contém VMs a partir de localizações geográficas vários, em seguida, o Centro de segurança cria várias áreas de trabalho. Várias áreas de trabalho são criadas para manter as regras da privacidade de dados.
-   Centro de segurança habilitará automaticamente uma solução de centro de segurança na área de trabalho pelo escalão de preço definido para a subscrição. 

> [!NOTE]
> Escalão de áreas de trabalho criadas pelo centro de segurança de preços do log analytics não afeta a faturação do Centro de segurança. A faturação do Centro de segurança baseia-se sempre em sua política de segurança do Centro de segurança e as soluções instaladas numa área de trabalho. Para o escalão gratuito, o Centro de segurança permite que o *SecurityCenterFree* solução na área de trabalho predefinida. No escalão Standard, o Centro de segurança permite que o *segurança* solução na área de trabalho predefinida.

Para obter mais informações sobre os preços, consulte [preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre contas de análise de registo existente, consulte [os clientes de análise do registo existente](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Utilizar uma área de trabalho existente

Se já tiver uma área de trabalho do Log Analytics existente que poderá pretender utilizar a mesma área de trabalho.

Para utilizar a sua área de trabalho do Log Analytics existente, tem de ter de leitura e escrita permissões na área de trabalho.

> [!NOTE]
> Soluções ativadas na área de trabalho existente serão aplicadas a VMs do Azure que estão ligados ao mesmo. Para as soluções pagas, isto pode resultar em encargos adicionais. Para considerações de privacidade de dados, certificar-se de que sua área de trabalho selecionada está na região geográfica certa.
>

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

   - Selecione **não** se pretender que as novas definições de área de trabalho para aplicar apenas a novas VMs. As novas definições de área de trabalho aplicam-se apenas a novas instalações de agentes; VMs detetadas recentemente que não têm o Microsoft Monitoring Agent instalado.
   - Selecione **Sim** se pretender que as novas definições de área de trabalho para aplicar em todas as VMs. Além disso, todas as VMS ligadas a um centro de segurança criada a área de trabalho for reconectada para a nova área de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, não tem de eliminar as áreas de trabalho criadas pelo centro de segurança até que todas as VMs tenham sido reconectadas à nova área de trabalho de destino. Esta operação falha se uma área de trabalho é eliminada demasiado antigo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecione a área de trabalho existente][3]

5. Selecione o escalão de preço para a área de trabalho pretendida que pretende definir o Microsoft Monitoring agent. <br>Para utilizar uma área de trabalho existente, defina o escalão de preço para a área de trabalho. Esta ação irá instalar uma solução de centro de segurança na área de trabalho, se já não estiver presente.

    a.  No menu principal do Centro de segurança, selecione **política de segurança**.
     
    b.  Selecione a área de trabalho pretendida na qual pretende ligar o agente ao clicar em **editar as definições de** na coluna Settings da subscrição pretendida na lista.
        ![Selecione a área de trabalho][8] c. Defina o escalão de preço.
        ![Selecione o escalão de preço][9] 
   
   >[!NOTE]
   >Se já tiver uma área de trabalho de um **Security** ou **SecurityCenterFree** solução ativada, os preços serão definidos automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de área de trabalho entre subscrições
Quando seleciona uma área de trabalho para armazenar seus dados, todas as áreas de trabalho em todas as suas subscrições estão disponíveis. Seleção de área de trabalho de subscrição cruzada permite-lhe recolher dados de máquinas virtuais em execução em diferentes subscrições e armazená-la na área de trabalho da sua preferência. Esta seleção é útil se estiver a utilizar uma área de trabalho centralizada na sua organização e pretender utilizá-lo para a recolha de dados de segurança. Para obter mais informações sobre como gerir áreas de trabalho, consulte [gerir o acesso de área de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Camada de recolha de dados
Selecionar um escalão de recolha de dados no Centro de segurança do Azure só afetarão o armazenamento de eventos de segurança na sua área de trabalho do Log Analytics. O agente Log Analytics ainda irá recolher e analisar os eventos de segurança necessários para deteções de ameaças do Centro de segurança do Azure, independentemente de qual dos escalões de eventos de segurança optar por armazenar na sua área de trabalho do Log Analytics (se houver). Selecionar a opção para armazenar eventos de segurança na sua área de trabalho irá permitir a investigação, a pesquisa e a auditoria desses eventos na sua área de trabalho. 
> [!NOTE]
> Armazenamento de dados no log analytics, pode incorrer em custos adicionais para o armazenamento de dados, consulte a página de preços para obter mais detalhes.
>
Pode escolher o direito de política para as suas subscrições e áreas de trabalho do quatro conjuntos de eventos de filtragem para serem armazenados na sua área de trabalho: 

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

- **Mínimo** -Certifique-se de que este conjunto abrange apenas os eventos que podem indicar uma violação com êxito e eventos importantes que têm um volume muito baixo. Por exemplo, este conjunto contém utilizador login bem e mal-sucedidas (evento IDs 4624, 4625), mas ele não contém a fim de sessão que é importante para auditoria, mas não significativo para deteção e tem relativamente elevado volume. A maioria do volume de dados deste conjunto é os eventos de início de sessão e o evento de criação de processo (evento ID 4688).
- **Comuns** -fornecer uma trilha de auditoria de utilizador completo neste conjunto. Por exemplo, este conjunto contém inícios de sessão do utilizador e de fim de sessão do utilizador (evento ID 4634). Podemos incluir a auditoria de ações como alterações do grupo de segurança, operações de Kerberos do controlador de domínio de chaves e outros eventos que são recomendados por organizações do setor.

Eventos com um volume muito baixa foram incluídos em comum definir como a principal motivação para escolher ao longo de todos os eventos é reduzir o volume e não para filtrar os eventos específicos.

Aqui está uma divisão completa a segurança e do App Locker de IDs de evento para cada conjunto:

| Camada de dados | Indicadores de eventos recolhidos |
| --- | --- |
| Mínimo | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,461,4622, |
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
1. Sobre o **recolha de dados de política de segurança** painel, selecione a política de filtragem em **eventos de segurança**.
2. Selecione **Guardar**.

   ![Escolha a política de filtragem][5]

### O aprovisionamento automático em casos de uma instalação de agente preexistente <a name="preexisting"></a> 

Os seguintes casos de utilização especificar aprovisionar como automática funciona em casos quando já existe um agente ou a extensão instalada. 

- Agente do log Analytics está instalado na máquina, mas não como uma extensão<br>
Se o agente do Log Analytics é instalado diretamente na VM (não como uma extensão do Azure), o Centro de segurança não instala o agente Log Analytics. Pode ativar o aprovisionamento automático e selecione a área de trabalho de utilizador relevantes na configuração de aprovisionamento de automática de centro de segurança. Se escolher a mesma área de trabalho que a VM já está ligada para o agente existente será moldada com uma extensão de agente do Log Analytics. 

> [!NOTE]
> Se o SCOM 2012 a versão do agente estiver instalado, **não** ativar sobre o aprovisionamento automático. 

Para obter mais informações, consulte [o que acontece se um SCOM ou OMS direcionar o agente já está instalado na minha VM?](security-center-faq.md#scomomsinstalled)

-   Uma extensão de VM já existente está presente<br>
    - Centro de segurança oferece suporte a instalações de extensão existente e não substitui as ligações existentes. Centro de segurança armazena os dados da VM na área de trabalho já ligado e fornece proteção com base em soluções de ativadas na área de trabalho de segurança.   
    - Para ver a área de trabalho que a extensão existente está a enviar dados para executar o teste seja [valide a conectividade com o Centro de segurança do Azure](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Em alternativa, pode abrir as áreas de trabalho do Log Analytics, selecione uma área de trabalho, selecione a VM e examinar a ligação de agente do Log Analytics. 
    - Se tiver um ambiente em que o agente do Log Analytics está instalado em estações de trabalho do cliente e de relatórios numa área de trabalho do Log Analytics existentes, reveja a lista de [sistemas operativos suportados pelo centro de segurança do Azure](security-center-os-coverage.md) certificar-se o sistema operativo é suportado e ver [os clientes de análise do registo existente](security-center-faq.md#existingloganalyticscust) para obter mais informações.
 
### Desativar aprovisionamento automático <a name="offprovisioning"></a>
Pode desativar aprovisionamento automático de recursos em qualquer altura ao desativar esta definição na política de segurança. 


1. Regresse ao menu principal do Centro de segurança e selecione a política de segurança.
2. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático.
3. Sobre o **política de segurança – recolha de dados** painel, em **aprovisionamento automático** selecionar **desativar**.
4. Selecione **Guardar**.

  ![Desativar aprovisionamento automático][6]

Quando o aprovisionamento automático está desativado (desativado), a seção de configuração de área de trabalho predefinido não é apresentada.

Se optar por desativar aprovisionamento automático após estava anteriormente em:
-   Agentes não serão aprovisionados em novas VMs.
-   Centro de segurança para a recolha de dados de área de trabalho predefinida.
 
> [!NOTE]
>  Desativar o aprovisionamento automático não remove o Microsoft Monitoring Agent VMS do Azure onde o agente foi aprovisionado. Para informações sobre como remover a extensão do OMS, consulte [como posso remover extensões OMS instaladas pelo centro de segurança](security-center-faq.md#remove-oms).
>
    
## Manual do agente de aprovisionamento <a name="manualagent"></a>
 
Existem várias formas de instalar manualmente o Microsoft Monitoring Agent. Ao instalar manualmente, certifique-se de que desativa aprovisionamento automático.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementação de extensão de VM de pacote de gestão de operações 

Pode instalar manualmente o agente de monitorização Microsoft, para que o Centro de segurança pode recolher dados de segurança das suas VMs e fornecer recomendações e alertas.
1.  Selecione o aprovisionamento automático – DESATIVADO.
2.  Criar uma área de trabalho e definir o escalão de preço para a área de trabalho que pretende definir o Microsoft Monitoring agent:

    a.  No menu principal do Centro de segurança, selecione **política de segurança**.
     
    b.  Selecione a área de trabalho no qual pretende ligar o agente. Certificar-se de que a área de trabalho está na mesma subscrição, utilize no Centro de segurança e que tem permissões de leitura/escrita na área de trabalho.
        ![Selecione a área de trabalho][8]
3. Defina o escalão de preço.
   ![Selecione o escalão de preço][9] 
   >[!NOTE]
   >Se já tiver uma área de trabalho de um **Security** ou **SecurityCenterFree** solução ativada, os preços serão definidos automaticamente. 
   > 

4.  Se quiser implantar os agentes nos novas VMs utilizando um modelo do Resource Manager, instale a extensão de máquina virtual do OMS:

    a.  [Instalar a extensão de máquina virtual do OMS para Windows](../virtual-machines/extensions/oms-windows.md)
    
    b.  [Instalar a extensão de máquina virtual do OMS para Linux](../virtual-machines/extensions/oms-linux.md)
5.  Para implementar as extensões em VMs existentes, siga as instruções em [recolher dados sobre máquinas virtuais do Azure](../azure-monitor/learn/quick-collect-azurevm.md).

  > [!NOTE]
  > A secção **recolher dados de eventos e desempenho** é opcional.
  >
6. Para utilizar o PowerShell para implementar a extensão, utilize o seguinte exemplo do PowerShell:  [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
    1.  Aceda a **do Log Analytics** e clique em **definições avançadas**.
    
        ![Conjunto o log analytics][11]

    2. Copie os valores de **WorkspaceID** e **chave primária**.
  
       ![Copiar valores][12]

    3. Preencha a configuração pública e a configuração privada com estes valores:
     
            $PublicConf = '{
                "workspaceId": "WorkspaceID value",
                "MultipleConnections": true
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

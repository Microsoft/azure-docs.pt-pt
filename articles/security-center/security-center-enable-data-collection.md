---
title: Recolha de dados no Centro de Segurança Azure Microsoft Docs
description: Este artigo descreve como instalar um Agente de Análise de Registos e definir um espaço de trabalho de Log Analytics para armazenar os dados recolhidos.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 5236fd89ed99ca14bb7fc24e40ef79e12e5177d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245502"
---
# <a name="data-collection-in-azure-security-center"></a>Data collection in Azure Security Center (Recolha de dados no Centro de Segurança do Azure)
O Security Center recolhe dados das suas máquinas virtuais Azure (VMs), conjuntos de escala de máquinas virtuais, contentores IaaS e computadores não-Azure (incluindo no local) para monitorizar vulnerabilidades e ameaças de segurança. Os dados são recolhidos através do Agente Delog Analytics, que lê várias configurações relacionadas com a segurança e registos de eventos da máquina e copia os dados para o seu espaço de trabalho para análise. Exemplos desses dados são: tipo e versão do sistema operativo, registos do sistema operativo (registos de eventos do Windows), processos de execução, nome da máquina, endereços IP e registos registados no utilizador. O Agente Log Analytics também copia ficheiros de despejo de crash para o seu espaço de trabalho.

A recolha de dados é necessária para fornecer visibilidade em atualizações em falta, configurações de segurança soco configuradas mal configuradas, estado de proteção de pontos finais e proteção contra a saúde e ameaças. 

Este artigo descreve como instalar um Agente de Análise de Registos e definir um espaço de trabalho de Log Analytics para armazenar os dados recolhidos. Ambas as operações são necessárias para permitir a recolha de dados. 

> [!NOTE]
> - A recolha de dados só é necessária para os recursos da Compute (VMs, conjuntos de escala de máquinas virtuais, contentores IaaS e computadores não-Azure). Você pode beneficiar do Azure Security Center mesmo que você não fornecer agentes; no entanto, terá segurança limitada e as capacidades acima listadas não são suportadas.  
> - Para a lista de plataformas suportadas, consulte [plataformas suportadas no Azure Security Center.](security-center-os-coverage.md)
> - Armazenar dados no Log Analytics, quer utilize um espaço de trabalho novo ou existente, poderá incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Ativar o fornecimento automático do Agente de Análise de Registos<a name="auto-provision-mma"></a>

Para recolher os dados das máquinas, deverá instalar o Agente de Análise de Registos. A instalação do agente pode ser feita automaticamente (recomendado) ou pode instalar o agente manualmente.  

>[!NOTE]
> O fornecimento automático está desligado por defeito. Para definir o Security Center para instalar o fornecimento automático por padrão, desloque-o para **On**.
>

Quando o fornecimento automático está ligado, o Security Center disponibiliza o Agente DeLog Analytics em todos os VMs Azure suportados e quaisquer novos que sejam criados. O fornecimento automático é fortemente recomendado, mas a instalação de agente manual também está disponível. [Saiba como instalar a extensão do Agente De Analítico de Log](#manual-agent).



Para permitir o fornecimento automático do Agente de Análise de Registos:
1. No menu principal do Security Center, selecione **definições de preços &**.
2. Clique na subscrição aplicável

   ![Selecionar subscrição][7]

3. Selecione **Recolha de Dados**.
4. No fornecimento **automático,** selecione **On** para ativar o fornecimento automático.
5. Selecione **Guardar**.

   ![Ativar o aprovisionamento automático][1]

>[!NOTE]
> - Para obter instruções sobre como fornecer uma instalação pré-existente, consulte o [fornecimento automático em caso de instalação de um agente pré-existente](#preexisting).
> - Para obter instruções sobre o fornecimento manual, consulte [Instale manualmente a extensão do Agente Desativação](#manual-agent).
> - Para obter instruções sobre o desativamento automático, consulte [desligar o fornecimento automático](#offprovisioning).
> - Para obter instruções sobre como embarcar no Centro de Segurança utilizando powerShell, consulte [o auto-embarque do Azure Security Center utilizando o PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuração do espaço de trabalho
Os dados recolhidos pelo Centro de Segurança são armazenados nas áreas de trabalho do Log Analytics. Pode selecionar para ter dados recolhidos a partir de VMs Azure armazenados em espaços de trabalho criados pelo Security Center ou num espaço de trabalho existente que criou. 

A configuração do espaço de trabalho é definida por subscrição, e muitas subscrições podem usar o mesmo espaço de trabalho.

### <a name="using-a-workspace-created-by-security-center"></a>Usando um espaço de trabalho criado pelo Security Center

O centro de segurança pode criar automaticamente um espaço de trabalho padrão para armazenar os dados. 

Para selecionar um espaço de trabalho criado pelo Security Center:

1. Sob **a configuração padrão do espaço**de trabalho, selecione Utilize espaço de trabalho criado pelo Centro de Segurança.
   ![Selecionar nível de preços][10] 

1. Clique em **Guardar**.<br>
    O Security Center cria um novo grupo de recursos e um espaço de trabalho padrão nessa geolocalização, e liga o agente a esse espaço de trabalho. A convenção de nomeação para o grupo de trabalho e recursos é:<br>
   **Espaço de trabalho: DefaultWorkspace-[subscrição-ID]-[geo]<br> Grupo de recursos: DefaultResourceGroup-[geo]**

   Se uma subscrição contiver VMs de múltiplas geolocalizações, então o Security Center cria vários espaços de trabalho. São criados vários espaços de trabalho para manter as regras de privacidade dos dados.
1. O Security Center ativará automaticamente uma solução de Centro de Segurança no espaço de trabalho de acordo com o nível de preços definido para a subscrição. 

> [!NOTE]
> O nível de preços log Analytics de espaços de trabalho criados pelo Security Center não afeta a faturação do Security Center. A faturação do Centro de Segurança baseia-se sempre na sua política de segurança do Centro de Segurança e nas soluções instaladas numa área de trabalho. Para o escalão Gratuito, o Centro de Segurança ativa a solução *SecurityCenterFree* na área de trabalho predefinida. Para o escalão Standard, o Centro de Segurança ativa a solução *Security* na área de trabalho predefinida.
> Armazenar dados no Log Analytics pode incorrer em encargos adicionais para o armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações sobre as contas de análise de registo existentes, consulte os clientes existentes de [análise de registo.](./faq-azure-monitor-logs.md)

### <a name="using-an-existing-workspace"></a>Usando um espaço de trabalho existente

Se já tiver um espaço de trabalho de Log Analytics existente, poderá querer utilizar o mesmo espaço de trabalho.

Para utilizar o espaço de trabalho existente no Log Analytics, deve ter lido e escrito permissões no espaço de trabalho.

> [!NOTE]
> As soluções ativadas no espaço de trabalho existente serão aplicadas aos VMs Azure que lhe estejam ligados. Para soluções pagas, isto poderia resultar em encargos adicionais. Para considerações de privacidade de dados, certifique-se de que o seu espaço de trabalho selecionado está na região geográfica certa.
> Armazenar dados em análise de registo pode incorrer em encargos adicionais para armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Para selecionar um espaço de trabalho existente no Log Analytics:

1. Sob a configuração do espaço de **trabalho Predefinido,** selecione **Utilize outro espaço de trabalho**.

   ![Selecione espaço de trabalho existente][2]

2. A partir do menu de pull-down, selecione um espaço de trabalho para armazenar dados recolhidos.

   > [!NOTE]
   > No menu pull down, todos os espaços de trabalho em todas as suas subscrições estão disponíveis. Consulte a [seleção de espaço de trabalho](security-center-enable-data-collection.md#cross-subscription-workspace-selection) de subscrição cruzada para obter mais informações. Deve ter permissão para aceder ao espaço de trabalho.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **Save,** será-lhe perguntado se gostaria de reconfigurar VMs monitorizados que estavam anteriormente ligados a um espaço de trabalho predefinido.

   - Selecione **Não** se pretender que as novas definições do espaço de trabalho se apliquem apenas em novos VMs. As novas definições do espaço de trabalho aplicam-se apenas às instalações de novos agentes; VMs recém-descobertos que não têm o Agente de Análise de Log instalado.
   - Selecione **Sim** se quiser que as novas definições do espaço de trabalho se apliquem em todos os VMs. Além disso, cada VM ligado a um centro de segurança criado espaço de trabalho é reconectado ao novo espaço de trabalho alvo.

   > [!NOTE]
   > Se selecionar Sim, não deve eliminar o espaço de trabalho criado pelo Centro de Segurança até que todos os VMs tenham sido religados ao novo espaço de trabalho alvo. Esta operação falha se um espaço de trabalho for apagado demasiado cedo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

     ![Selecione espaço de trabalho existente][3]

5. Selecione o nível de preços para o espaço de trabalho desejado que pretende definir o Agente De Análise de Log. <br>Para utilizar um espaço de trabalho existente, detete o nível de preços para o espaço de trabalho. Isto irá instalar uma solução de Centro de Segurança no espaço de trabalho se não estiver já presente.

    a.  No menu principal do Security Center, **selecione definições de preços &**.
     
    b.  Selecione o espaço de trabalho desejado no qual pretende ligar o agente.
        ![Selecione][7] espaço de trabalho c. Desloque o nível de preços.
        ![Selecionar nível de preços][9]
   
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução **Security** ou **SecurityCenterFree** ativada, o preço será definido automaticamente. 

## <a name="cross-subscription-workspace-selection"></a>Seleção de espaço de trabalho de subscrição cruzada
Quando seleciona um espaço de trabalho para armazenar os seus dados, todos os espaços de trabalho em todas as suas subscrições estão disponíveis. A seleção da área de trabalho de subscrições cruzadas permite-lhe recolher dados de máquinas virtuais em execução em diferentes subscrições e armazenar os mesmos na área de trabalho da sua preferência. Esta seleção será útil se estiver a utilizar uma área de trabalho centralizada na sua organização e pretender utilizá-la para a recolha de dados de segurança. Para mais informações sobre como gerir espaços de trabalho, consulte [Gerir o acesso ao espaço de trabalho.](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)


## <a name="data-collection-tier"></a>Nível de recolha de dados
A seleção de um escalão de recolha de dados no Centro de Segurança do Azure só vai afetar o armazenamento dos eventos de segurança na área de trabalho do Log Analytics. O agente Log Analytics ainda irá recolher e analisar os eventos de segurança necessários para a proteção contra ameaças do Azure Security Center, independentemente do nível de eventos de segurança que escolha armazenar no seu espaço de trabalho Log Analytics (se houver). Optar por armazenar os eventos de segurança na sua área de trabalho permitirá a investigação, a pesquisa e a auditoria desses eventos na área de trabalho. 
> [!NOTE]
> Armazenar dados em análise de registo pode incorrer em encargos adicionais para armazenamento de dados. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).
> 
> Pode escolher a política de filtragem certa para as suas subscrições e espaços de trabalho a partir de quatro conjuntos de eventos a serem armazenados no seu espaço de trabalho: 

- **Nenhum** – Desativar o armazenamento de eventos de segurança. Esta é a predefinição.
- **Mínimo** – Um conjunto menor de eventos para clientes que queiram minimizar o volume do evento.
- **Common** – Este é um conjunto de eventos que satisfaz a maioria dos clientes e lhes permite um rasto de auditoria completo.
- **Todos os eventos** – Para clientes que queiram certificar-se de que todos os eventos estão armazenados.


> [!NOTE]
> Estes conjuntos de eventos de segurança estão disponíveis apenas no nível Standard do Security Center. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preços do Centro de Segurança.
Estes conjuntos foram concebidos para abordar cenários típicos. Certifique-se de avaliar qual se adequa às suas necessidades antes de implementá-la.
>
>

Para determinar os eventos que pertencerão aos conjuntos de eventos **Common** e **Minimal,** trabalhámos com clientes e padrões da indústria para aprender sobre a frequência não filtrada de cada evento e a sua utilização. Usámos as seguintes orientações neste processo:

- **Minimal** - Certifique-se de que este conjunto abrange apenas eventos que possam indicar uma violação bem sucedida e eventos importantes que tenham um volume muito baixo. Por exemplo, este conjunto contém login bem sucedido e falhado do utilizador (evento IDs 4624, 4625), mas não contém sinal que é importante para a auditoria, mas não significativo para a deteção e tem um volume relativamente elevado. A maior parte do volume de dados deste conjunto é o evento de login e evento de criação de processos (id do evento 4688).
- **Common** - Forneça um rasto completo de auditoria ao utilizador neste conjunto. Por exemplo, este conjunto contém sessão de sessão do utilizador e inscrições do utilizador (ID do evento 4634). Incluímos ações de auditoria como alterações de grupos de segurança, operações principais do controlador de domínio Kerberos, e outros eventos que são recomendados pelas organizações do setor.

Eventos com um volume muito baixo foram incluídos no conjunto comum como a principal motivação para escolhê-lo em todos os eventos é reduzir o volume e não filtrar eventos específicos.

Aqui está uma desagregação completa das IDs do evento Security e App Locker para cada conjunto:

| Camada física de dados | Indicadores de eventos recolhidos |
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
> - Se estiver a utilizar o Objeto de Política de Grupo (GPO), recomenda-se que ative as políticas de auditoria Process Creation Event 4688 e o campo *CommandLine* dentro do evento 4688. Para mais informações sobre o Evento de Criação de Processos 4688, consulte as [FAQ](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)do Centro de Segurança. Para obter mais informações sobre estas políticas de auditoria, consulte recomendações de política de [auditoria.](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)
> -  Para permitir a recolha de dados para controlos de [aplicações adaptáveis,](security-center-adaptive-application.md)o Security Center configura uma política local do AppLocker no modo de Auditoria para permitir todas as aplicações. Isto fará com que o AppLocker gere eventos que são depois recolhidos e alavancados pelo Security Center. É importante notar que esta política não será configurada em nenhuma máquina em que já exista uma política configurada do AppLocker. 
> - Para recolher o Windows Filtering Platform [Event ID 5156,](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)é necessário ativar a ligação da plataforma de [filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) de auditoria (Auditpol /set /subcategoria:"Filtering Platform Connection" /Success:Enable)
>

Para escolher a sua política de filtragem:
1. Na página de Recolha de **Dados,** selecione a sua política de filtragem em **Eventos**de Segurança .
2. Selecione **Guardar**.

   ![Escolha a política de filtragem][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Fornecimento automático em caso de instalação de agente pré-existente<a name="preexisting"></a> 

Os seguintes casos de utilização especificam como funciona a provisão automática nos casos em que já existe um agente ou extensão instalado. 

- O Agente de Análise de Registos está instalado na máquina, mas não como uma extensão (agente direto)<br>
Se o Agente De log analytics estiver instalado diretamente no VM (não como uma extensão Azure), o Security Center instalará a extensão do Agente DeLog Analytics e poderá atualizar o Agente de Análise de Registo para a versão mais recente.
O agente instalado continuará a reportar ao seu espaço de trabalho já configurado, e além disso reportará ao espaço de trabalho configurado no Security Center (Multi-homing é suportado em máquinas Windows).
Se o espaço de trabalho configurado for um espaço de trabalho do utilizador (não o espaço de trabalho padrão do Security Center), então terá de instalar a solução "security/"securityFree" nele para o Security Center começar a processar eventos a partir de VMs e computadores que reportem a esse espaço de trabalho.<br>
<br>
Para as máquinas Linux, o agente multi-homing ainda não é suportado - portanto, se for detetada uma instalação de agente existente, não ocorrerá um fornecimento automático e a configuração da máquina não será alterada.
<br>
Para máquinas existentes em assinaturas a bordo do Centro de Segurança antes de 2019-03-17, quando um agente existente for detetado, a extensão do Agente de Análise de Log não será instalada e a máquina não será afetada. Para estas máquinas, consulte a recomendação "Resolver problemas de saúde do agente de monitorização nas suas máquinas" para resolver os problemas de instalação do agente nestas máquinas.

  
- O agente do Gestor de Operações do Centro de Sistemas está instalado na máquina<br>
O centro de segurança instalará a extensão do Agente DeLog Analytics lado a lado para o gestor de operações existente. O agente do Gestor de Operações existente continuará a reportar normalmente ao servidor do Gestor de Operações. Note que o agente do Gestor de Operações e o Agente DeLog Analytics partilham bibliotecas comuns de tempo de execução, que serão atualizadas para a versão mais recente durante este processo.
Nota - Se a versão 2012 do agente do Gestor de Operações estiver instalada, **não** ligue o fornecimento automático.<br>

- Uma extensão VM pré-existente está presente<br>
    - Quando o Agente de Monitorização é instalado como uma extensão, a configuração da extensão permite reportar apenas um único espaço de trabalho. O Security Center não sobrepor-se às ligações existentes aos espaços de trabalho dos utilizadores. O Security Center armazenará dados de segurança do VM no espaço de trabalho já ligado, desde que a solução "security" ou "securityFree" tenha sido instalada no mesmo. O Security Center pode atualizar a versão de extensão para a versão mais recente neste processo.  
    - Para ver para que espaço de trabalho a extensão existente está a enviar dados, execute o teste para validar a conectividade com o [Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). Em alternativa, pode abrir espaços de trabalho do Log Analytics, selecionar um espaço de trabalho, selecionar o VM e ver a ligação do agente Log Analytics. 
    - Se tiver um ambiente em que o agente Log Analytics esteja instalado em postos de trabalho de clientes e reportando a um espaço de trabalho existente no Log Analytics, reveja a lista de [sistemas operativos suportados pelo Azure Security Center](security-center-os-coverage.md) para garantir que o seu sistema operativo é suportado. Para mais informações, consulte os clientes existentes de [análise de registo.](./faq-azure-monitor-logs.md)
 
### <a name="turn-off-automatic-provisioning"></a>Desligue o fornecimento automático<a name="offprovisioning"></a>
Pode desligar o fornecimento automático de recursos a qualquer momento, desligando esta definição na política de segurança. 


1. Volte ao menu principal do Centro de Segurança e selecione a política de segurança.
2. Clique em **configurações de Edição** na linha da subscrição para a qual pretende desativar o fornecimento automático.
3. Sobre a política de segurança – lâmina de recolha de **dados,** sob **o fornecimento automático** selecione **Off**.
4. Selecione **Guardar**.

   ![Desativar o fornecimento de automóveis][6]

Quando o fornecimento automático é desativado (desligado), a secção de configuração do espaço de trabalho por defeito não é visualizada.

Se desligar a provisão automática depois de ter sido previamente ligada:
-   Os agentes não serão aprovisionados em novos VMs.
-   O Centro de Segurança deixa de recolher dados do espaço de trabalho padrão.
 
> [!NOTE]
>  A desativação automática não remove o Agente de Análise de Log dos VMs Azure onde o agente foi provisionado. Para obter informações sobre a remoção da extensão OMS, consulte [como remover as extensões OMS instaladas pelo Security Center](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Fornecimento de agentes manuais<a name="manual-agent"></a>
 
Existem várias formas de instalar manualmente o Agente de Análise de Registos. Ao instalar manualmente, certifique-se de que desativa o fornecimento automático.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementação de extensão de extensão VM da Suite de Gestão de Operações 

Pode instalar manualmente o Agente de Análise de Registos, para que o Security Center possa recolher dados de segurança dos seus VMs e fornecer recomendações e alertas.
1. Selecione fornecimento automático - OFF.
2. Crie um espaço de trabalho e detete teo nível de preços para o espaço de trabalho que pretende definir o Agente de Análise de Log:

   a.  No menu principal do Centro de Segurança, selecione a política de **segurança.**
     
   b.  Selecione o Espaço de Trabalho no qual pretende ligar o agente. Certifique-se de que o espaço de trabalho está na mesma subscrição que utiliza no Centro de Segurança e que tem permissões de leitura/escrita no espaço de trabalho.
       ![Selecionar área de trabalho][8]
3. Desloque o nível de preços.
   ![Selecionar nível de preços][9] 
   >[!NOTE]
   >Se o espaço de trabalho já tiver uma solução **Security** ou **SecurityCenterFree** ativada, o preço será definido automaticamente. 
   > 

4. Se pretender implantar os agentes em novos VMs utilizando um modelo de Gestor de Recursos, instale a extensão da máquina virtual OMS:

   a.  [Instale a extensão da máquina virtual OMS para windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Instale a extensão da máquina virtual OMS para linux](../virtual-machines/extensions/oms-linux.md)
5. Para implementar as extensões dos VMexistentes, siga as instruções em [Recolher dados sobre máquinas virtuais Azure](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > A secção Recolher dados de **eventos e desempenho** é opcional.
   >
6. Para utilizar o PowerShell para implementar a extensão, utilize o seguinte exemplo PowerShell:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Vá ao **Log Analytics** e clique em **definições avançadas**.
    
      ![Definir análise de registo][11]

   2. Copie os valores a partir do **WorkspaceID** e da **tecla Primária**.
  
      ![Valores de cópia][12]

   3. Povoar o config público e o config privado com estes valores:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Ao instalar num VM do Windows:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Ao instalar num VM Linux:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Para obter instruções sobre como embarcar no Centro de Segurança utilizando powerShell, consulte [o auto-embarque do Azure Security Center utilizando o PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Resolução de problemas

-   Para identificar problemas de instalação automáticas, consulte problemas de [saúde dos agentes de monitorização.](security-center-troubleshooting-guide.md#mon-agent)

-  Para identificar os requisitos da rede de agentes de monitorização, consulte os requisitos de rede do agente de monitorização de [resolução de problemas](security-center-troubleshooting-guide.md#mon-network-req).
-   Para identificar problemas de embarque manual, consulte como resolver problemas na Suite de [Gestão de Operações](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Para identificar vms não monitorizados e problemas de computadores:

    Um VM ou computador não é monitorizado pelo Security Center se a máquina não estiver a executar a extensão do Microsoft Monitoring Agent. Uma máquina pode ter um agente local já instalado, por exemplo, o agente direto OMS ou o agente gestor de operações do System Center. As máquinas com estes agentes são identificadas como não monitorizadas porque estes agentes não estão totalmente suportados no Centro de Segurança. Para beneficiar totalmente de todas as capacidades do Centro de Segurança, é necessária a extensão MMA.

    Para obter mais informações sobre as razões pelas quais o Security Center não consegue monitorizar com sucesso VMs e computadores inicialmente para o fornecimento automático, consulte problemas de [saúde do agente de monitorização](security-center-troubleshooting-guide.md#mon-agent).


## <a name="next-steps"></a>Passos seguintes
Este artigo mostrou-lhe como funciona a recolha de dados e o fornecimento automático no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [FAQ do Centro de Segurança do Azure](faq-general.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
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

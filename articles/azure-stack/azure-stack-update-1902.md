---
title: Atualização de 1902 de pilha do Azure | Documentos da Microsoft
description: Saiba mais sobre a atualização de 1902 para o sistema integrado do Azure Stack, incluindo o que há de novo, onde pode transferir a atualização e problemas conhecidos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 66dfdf3a88a4bacdc118fed00d79f02b22da7869
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792468"
---
# <a name="azure-stack-1902-update"></a>Atualização de 1902 de pilha do Azure

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1902. A atualização inclui melhorias, correções e novas funcionalidades para esta versão do Azure Stack. Este artigo também descreve os problemas conhecidos nesta versão e inclui uma ligação para transferir a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados com o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é apenas para sistemas integrados do Azure Stack. Não é aplicável este pacote de atualização para o Azure Stack Development Kit.

## <a name="build-reference"></a>Criar a referência

É o número de compilação de atualização do Azure Stack 1902 **1.1902.0.69**.

## <a name="hotfixes"></a>Correções

O Azure Stack lança correções em intervalos regulares. Certifique-se de que instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1901 antes de atualizar o Azure Stack para 1902.

Correções de pilha do Azure só são aplicáveis a sistemas integrados do Azure Stack; Não tente instalar correções no ASDK.

> [!TIP]  
> Subscrever o seguinte procedimento *RSS* ou *Atom* feeds para se manter atualizado com correções do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correções de pilha do Azure

- **1809**: [KB 4481548 – o Azure Stack correção 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Sem correções atual disponível.
- **1901**: Sem correções atual disponível.
- **1902**: Sem correções atual disponível.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
- Instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1901 (se houver) antes de atualizar para 1902.

- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com os parâmetros seguintes para validar o status do seu Azure Stack e resolver quaisquer problemas operacionais detectados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação:

    ```PowerShell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Quando o Azure Stack é gerido pelo System Center Operations Manager (SCOM), certifique-se de atualizar o [pacote de gestão para o Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) para versão 1.0.3.11 antes de aplicar 1902.

- O formato de pacote para a atualização do Azure Stack foi alterado de **.bin/.exe/.xml** ao **.zip/.xml** a partir da versão 1902. Serão mostrados aos clientes com unidades de escala do Azure Stack ligados a **atualização disponível** mensagem no portal. Os clientes que não estão ligados podem agora transferir simplesmente e importar o ficheiro. zip com o ficheiro. xml correspondente.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Melhorias

- A compilação de 1902 introduz uma nova interface de utilizador no portal do administrador do Azure Stack para a criação de planos, ofertas, quotas e planos de suplementos. Para obter mais informações, incluindo capturas de ecrã, consulte [criar planos, ofertas e quotas](azure-stack-create-plan.md).

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Para melhorar a integridade do pacote e segurança, bem como gestão mais simples para ingestão offline, a Microsoft alterou o formato do pacote de atualização de ficheiros. .exe e. bin para um ficheiro. zip. O novo formato adiciona mais confiabilidade do processo de descompactação que às vezes, podem causar a preparação da atualização estagne. O mesmo formato de pacote também se aplica ao atualizar a partir de seu OEM.
- Para melhorar a experiência do operador do Azure Stack ao executar o teste AzureStack, operadores agora basta usar, "Test-AzureStack-grupo UpdateReadiness" em vez de passar parâmetros adicionais dez após uma instrução de inclusão.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Para melhorar a fiabilidade e disponibilidade dos serviços de infraestrutura de núcleo geral durante o processo de atualização, o fornecedor de recursos de atualização nativo como parte do plano de ação de atualização irá detetar e invocar automática remediações global, conforme necessário. Fluxos de trabalho de remediação global "reparar" incluem:
    - Verificar a existência de máquinas de virtuais de infraestrutura que estão num Estado não ideal e tentar repará-lo conforme necessário 
    - Verifique a existência de problemas de serviço do SQL como parte do plano de controle e tentar repará-lo conforme necessário
    - Verifique o estado do serviço de Balanceador de carga de Software (SLB) como parte do controlador de rede (NC) e tentar repará-lo conforme necessário
    - Verifique o estado do serviço de controlador de rede (NC) e tentam repará-la conforme necessário
    - Verifique o estado de nós de recursos de infraestrutura do serviço do serviço de consola de recuperação de emergência (ERCS) e repará-los, conforme necessário
    - Verifique o estado de nós de recursos de infraestrutura do serviço XRP e repará-los, conforme necessário
    - Verifique o estado de nós de recursos de infraestrutura do serviço de armazenamento consistente do Azure (ACS) e repará-los, conforme necessário



## <a name="common-vulnerabilities-and-exposures"></a>Vulnerabilidades e exposições comuns

Esta atualização instala as seguintes atualizações de segurança:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)


Para obter mais informações sobre essas vulnerabilidades, clique nos links anteriores ou veja os artigos da Base de dados de conhecimento da Microsoft [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando executa [AzureStack teste](azure-stack-diagnostic-test.md), será apresentada uma mensagem de aviso do controlador de gestão de placas base (BMC). Pode ignorar este aviso.

- <!-- 2468613 - IS --> Durante a instalação desta atualização, poderá ver alertas com o título `Error – Template for FaultType UserAccounts.New is missing.` pode ignorar com segurança estes alertas. Os alertas fechar automaticamente depois de concluída a instalação desta atualização.

## <a name="post-update-steps"></a>Passos de pós-atualização

- Após a instalação desta atualização, instale as correções aplicáveis. Para obter mais informações, consulte [correções](#hotfixes), bem como nossos [política de manutenção](azure-stack-servicing-policy.md).  

- Obter os dados com as chaves de encriptação do rest e com segurança armazená-las fora da sua implementação do Azure Stack. Siga os [instruções sobre como obter as chaves](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

Seguem-se após a instalação problemas conhecidos para esta versão de compilação.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- Nos portais do administrador e o utilizador, se pesquisar por "Docker", o item incorretamente é devolvido. Não está disponível no Azure Stack. Se tentar criá-lo, é apresentado um painel com uma indicação de erro. 

<!-- 2931230 – IS  ASDK --> 
- Não não possível eliminar os planos que são adicionados a uma subscrição de utilizador como um plano de suplemento, mesmo quando remover o plano de subscrição de utilizador. O plano irá permanecer até que as subscrições que referenciam o plano de suplemento também são eliminadas. 

<!-- TBD - IS ASDK --> 
- Os dois tipos de subscrição administrativas que foram introduzidos com versão 1804 não devem ser utilizados. Os tipos de subscrição são **medição subscrição**, e **subscrição de consumo**. Esses tipos de subscrição são visíveis no novos ambientes do Azure Stack a partir da versão 1804, mas ainda não estão prontos a utilizar. Deve continuar a utilizar o **fornecedor predefinido** tipo de subscrição.

<!-- 3557860 - IS ASDK --> 
- A eliminar os resultados de subscrições do utilizador em recursos órfãos. Como solução, primeiro eliminar recursos de utilizador ou grupo de recursos inteiro e, em seguida, elimine as subscrições de utilizador.

<!-- 1663805 - IS ASDK --> 
- Não é possível ver as permissões à sua subscrição a utilizar os portais do Azure Stack. Como solução, utilize [PowerShell para verificar as permissões](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Computação

- Ao criar um novo Windows Máquina Virtual (VM), poderá ser apresentado o erro seguinte:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se ativar diagnósticos de arranque numa VM, mas eliminar a sua conta de armazenamento de diagnósticos de arranque. Para contornar este problema, recrie a conta de armazenamento com o mesmo nome que utilizou anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A experiência de criação de conjunto (VMSS) de dimensionamento de máquina virtual fornece 7.2 baseada em CentOS, como uma opção para implementação. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para a sua implementação, ou utilizar um modelo do Azure Resource Manager, especificando outra imagem do CentOS que tenha sido transferida antes da implementação a partir do mercado, o operador.  

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1902 de atualização, poderá encontrar os seguintes problemas ao implementar VMs com discos geridos:

   - Se a subscrição tiver sido criada antes da atualização de 1808, implementar uma VM com Managed Disks poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos para cada subscrição:
      1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Selecione **fornecedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
      2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que **disco gerido do Azure Stack –** está listado.
   - Se tiver configurado o ambiente multi-inquilino, a implementação de VMs numa assinatura associada um diretório de convidado poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos [este artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) reconfigurar cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização de SSH ativada não permitirá que use as chaves SSH para iniciar sessão. Como solução, utilize o acesso VM para a extensão do Linux para implementar as chaves SSH após o aprovisionamento ou utilizar a autenticação baseada em palavra-passe.

- Crie 1902, a memória necessária pela infra-estrutura ERCS VM foi aumentou de 8 GB para 12 GB. Num ASDK, isso resulta num aumento de 4 GB. Numa instalação de sistemas integrados do Azure Stack, é um aumento de 12 GB.

   Para certificar-se de que o processo de patch e atualização resulta no menor período de tempo de inatividade do inquilino, certifique-se do carimbo de data do Azure Stack tem mais de 12 GB de espaço disponível na **capacidade** painel. Pode ver esta memória aumentar refletidas na **capacidade** painel após uma instalação com êxito da atualização.

### <a name="networking"></a>Redes  

<!-- 3239127 - IS, ASDK -->
- No portal do Azure Stack, quando altera um endereço IP estático para uma configuração de IP que está vinculado a um adaptador de rede anexado a uma instância VM, verá uma mensagem de aviso que indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Pode ignorar esta mensagem; o endereço IP será alterado, mesmo que a instância VM não é reinicializado.

<!-- 3632798 - IS, ASDK -->
- No portal, se adicionar uma regra de segurança de entrada e selecione **etiquetas de serviço** como a origem, várias opções são apresentadas no **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são os seguintes:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  As outras opções não são suportadas como etiquetas de origem no Azure Stack. Da mesma forma, se adicionar uma regra de segurança de saída e selecione **etiquetas de serviço** como o destino, a mesma lista de opções para **marca de origem** é apresentado. As opções apenas válidas são os mesmos que para **marca de origem**, conforme descrito na lista anterior.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack estar na mesma forma como global. No Azure, pode definir várias portas numa regra NSG (com o portal, PowerShell e modelos do Resource Manager). No Azure Stack, no entanto, não é possível definir várias portas numa regra NSG através do portal. Para contornar este problema, utilize um modelo do Resource Manager ou o PowerShell para definir estas regras adicionais.

<!-- 3203799 - IS, ASDK -->
- O Azure Stack não suporta a ligação a mais de 4 Interfaces de rede (NICs) para as instâncias VM de hoje em dia, independentemente do tamanho de instância.

- Foi identificado um problema em que os pacotes mais 1450 bytes para uma carga balanceador interno (ILB) são ignorados. O problema é devido à definição de MTU no anfitrião que está a ser demasiado baixa para acomodar os pacotes VXLAN encapsulado que atravessam a função, que, no momento da elaboração 1901 foi movida para o anfitrião. Existem, pelo menos, dois cenários que podem surgir em que temos visto este problema se manifestar:

  - Consultas SQL para o SQL Always-On que é protegido por uma carga balanceador interno (ILB) e são mais 660 bytes.
  - Implementações de Kubernetes falharem se tentar ativar vários modelos de estrutura mestres.  

  O problema ocorre quando tem a comunicação entre uma VM e um ILB na mesma rede virtual, mas em sub-redes diferentes. Pode contornar este problema ao executar os comandos seguintes numa linha de comandos elevada no anfitrião ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicações

<!-- 2352906 - IS ASDK --> 
- Tem de registar o fornecedor de recursos de armazenamento antes de criar a sua primeira função do Azure na subscrição.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização do Azure Stack 1902 partir [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários de ligação apenas, implementações do Azure Stack verificar periodicamente um ponto de extremidade seguro e notificam automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [gerir atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral da gestão de atualizações no Azure Stack, veja [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar as atualizações com o Azure Stack, veja [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).
- Para rever a política de manutenção para sistemas integrados do Azure Stack e o que deve fazer para manter seu sistema num estado suportado, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para utilizar o ponto final com privilégios (PEP) para monitorizar e retomar as atualizações, consulte [monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios](azure-stack-monitor-update.md).  

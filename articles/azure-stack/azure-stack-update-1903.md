---
title: Atualização de 1903 de pilha do Azure | Documentos da Microsoft
description: Saiba mais sobre a atualização de 1903 para o sistema integrado do Azure Stack, incluindo o que há de novo, onde pode transferir a atualização e problemas conhecidos.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/09/2019
ms.openlocfilehash: 79f61f99050748c93ca4bd17d1849f4cbba7a295
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360567"
---
# <a name="azure-stack-1903-update"></a>Atualização de 1903 de pilha do Azure

*Aplica-se a Sistemas integrados do Azure Stack*

Este artigo descreve o conteúdo do pacote de atualização de 1903. A atualização inclui melhorias, correções e novas funcionalidades para esta versão do Azure Stack. Este artigo também descreve os problemas conhecidos nesta versão e inclui uma ligação para transferir a atualização. Problemas conhecidos são divididos em problemas diretamente relacionados com o processo de atualização e problemas com a compilação (após a instalação).

> [!IMPORTANT]  
> Este pacote de atualização é apenas para sistemas integrados do Azure Stack. Não é aplicável este pacote de atualização para o Azure Stack Development Kit.

## <a name="build-reference"></a>Criar a referência

É o número de compilação de atualização do Azure Stack 1903 **1.1903.0.35**.

> [!IMPORTANT]
> O payload de 1903 não inclui uma versão ASDK.

## <a name="hotfixes"></a>Correções

O Azure Stack lança correções em intervalos regulares. Certifique-se de que instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1902 antes de atualizar o Azure Stack para 1903.

Correções de pilha do Azure só são aplicáveis a sistemas integrados do Azure Stack; Não tente instalar correções no ASDK.

> [!TIP]  
> Subscrever o seguinte procedimento *RSS* ou *Atom* feeds para se manter atualizado com correções do Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correções de pilha do Azure

- **1902**: [KB 4481548 – o Azure Stack correção 1.1902.2.73](https://support.microsoft.com/help/4494719)

## <a name="improvements"></a>Melhorias

- O payload de atualização de 1903 contém uma atualização nos componentes do Azure Stack que inclua o sistema operacional subjacente que anfitriões do Azure Stack. Isto permite que determinadas atualizações ser confinada. Como resultado, a esperada tempo necessário para concluir a atualização de 1903 é menor (aprox. 16 horas, mas os tempos exatos podem variar). Essa redução no tempo de execução é específica para a atualização de 1903 e atualizações subsequentes podem conter atualizações para o sistema operativo, implicando tempos de execução diferentes. As futuras atualizações fornecerá diretrizes semelhantes sobre o tempo esperado, que a atualização demora a concluir, consoante o payload incluído.

- Foi corrigido um erro no sistema de rede que impediam as alterações para o **tempo limite de inatividade (minutos)** valor de um **endereço IP público** de entrarem em vigor. Anteriormente, as alterações a este valor foram ignoradas, para que, independentemente de quaisquer alterações efetuadas, o valor seria predefinido a 4 minutos. Esta definição controla o número de minutos para manter uma conexão TCP abrir sem depender de clientes para enviar mensagens keep-alive. Observe que esse bug afetados apenas a instância de nível de IPs públicos, os IPs públicos não atribuído a um balanceador de carga.

- Melhoramentos de fiabilidade de que o mecanismo de atualização, incluindo a correção automática de problemas comuns, para que se aplicam as atualizações sem interrupção.

- Melhorias para a deteção e remediação de condições do espaço em disco insuficiente.

### <a name="secret-management"></a>Gestão de segredos

- O Azure Stack suporta agora a rotação do certificado de raiz utilizado por certificados para a rotação de secreta externa. Para obter mais informações, [veja este artigo](azure-stack-rotate-secrets.md).

- 1903 contém aprimoramentos de desempenho para a rotação de segredo que reduzem o tempo que demora a executar a rotação secreta interna.

## <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Instalar o [mais recente do Azure Stack correção](#azure-stack-hotfixes) para 1902 (se houver) antes de atualizar para 1903.

- Certifique-se utilizar a versão mais recente do [Planeador de capacidade do Azure Stack](https://aka.ms/azstackcapacityplanner) fazer sua carga de trabalho de planejamento e de dimensionamento. A versão mais recente contém correções de erros e fornece novos recursos que são lançados com cada atualização do Azure Stack.

- Antes de iniciar a instalação desta atualização, execute [AzureStack teste](azure-stack-diagnostic-test.md) com o parâmetro seguinte para validar o status do seu Azure Stack e resolva quaisquer problemas operacionais encontrados, incluindo todos os avisos e falhas. Também rever alertas ativos e resolver qualquer um que requerem uma ação:

    ```PowerShell
   Test-AzureStack -Group UpdateReadiness  
    ```

- Ao Azure Stack é gerido pelo System Center Operations Manager, certifique-se atualizar o [pacote de gestão para o Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) para versão 1.0.3.11 antes de aplicar 1903.

- O formato de pacote para a atualização do Azure Stack foi alterado de **.bin/.exe/.xml** ao **.zip/.xml** a partir da versão 1902. Serão mostrados aos clientes com unidades de escala do Azure Stack ligados a **atualização disponível** mensagem no portal. Os clientes que não estão ligados podem agora transferir simplesmente e importar o ficheiro. zip com o ficheiro. xml correspondente.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Problemas conhecidos com o processo de atualização

- Quando executa [AzureStack teste](azure-stack-diagnostic-test.md), será apresentada uma mensagem de aviso do controlador de gestão de placas base (BMC). Pode ignorar este aviso.

- <!-- 2468613 - IS --> Durante a instalação desta atualização, poderá ver alertas com o título **erro – o modelo para typu FaultType UserAccounts.New está em falta.** Pode ignorar com segurança estes alertas. Os alertas fechar automaticamente depois de concluída a instalação desta atualização.

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

<!-- Daniel 3/28 -->
- No portal de utilizador, ao navegar para um blob numa conta de armazenamento e tente abri **política de acesso** na árvore de navegação, a janela subsequente não consegue carregar.

<!-- Daniel 3/28 -->
- No portal de utilizador, ao tentar carregar um blob com o **OAuth(preview)** opção, a tarefa falhar com uma mensagem de erro. Para contornar este problema, carregar o blob com o **SAS** opção.

- Depois de iniciar sessão nos portais do Azure Stack poderá ver notificações sobre o portal do Azure público. Pode ignorar com segurança essas notificações, pois eles não aplicam-se atualmente ao Azure Stack (por exemplo, "1 nova atualização - as seguintes atualizações estão agora disponíveis: Atualização do Azure portal Abril de 2019").

<!-- ### Health and monitoring -->

### <a name="compute"></a>Computação

- Ao criar um novo Windows Máquina Virtual (VM), poderá ser apresentado o erro seguinte:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   O erro ocorre se ativar diagnósticos de arranque numa VM, mas eliminar a sua conta de armazenamento de diagnósticos de arranque. Para contornar este problema, recrie a conta de armazenamento com o mesmo nome que utilizou anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A experiência de criação de conjunto de dimensionamento de Máquina Virtual fornece 7.2 baseada em CentOS, como uma opção para implementação. Porque essa imagem não está disponível no Azure Stack, selecione outro sistema operacional para a sua implementação, ou utilizar um modelo do Azure Resource Manager, especificando outra imagem do CentOS que tenha sido transferida antes da implementação a partir do mercado, o operador.  

<!-- TBD - IS ASDK --> 
- Depois de aplicar o 1903 de atualização, poderá encontrar os seguintes problemas ao implementar VMs com discos geridos:

   - Se a subscrição tiver sido criada antes da atualização de 1808, implementar uma VM com Managed Disks poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos para cada subscrição:
      1. No portal do inquilino, aceda a **subscrições** e encontrar a subscrição. Selecione **fornecedores de recursos**, em seguida, selecione **Microsoft. Compute**e, em seguida, clique em **voltar a registar**.
      2. Na mesma subscrição, aceda a **controlo de acesso (IAM)** e certifique-se de que **disco gerido do Azure Stack –** está listado.
   - Se tiver configurado o ambiente multi-inquilino, a implementação de VMs numa assinatura associada um diretório de convidado poderá falhar com uma mensagem de erro interno. Para resolver o problema, siga estes passos [este artigo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) reconfigurar cada um dos seus diretórios de convidado.

- Uma VM do Ubuntu 18.04 criado com autorização de SSH ativada não permitirá que use as chaves SSH para iniciar sessão. Como solução, utilize o acesso VM para a extensão do Linux para implementar as chaves SSH após o aprovisionamento ou utilizar a autenticação baseada em palavra-passe.

- O Azure Stack agora oferece suporte a agentes do Windows Azure Linux superiores versão 2.2.20. Esse suporte foi uma parte da correção 1901 e 1902 e permite que os clientes manter as imagens do linux consistentes entre o Azure e o Azure Stack.


- Se não tiver um anfitrião de ciclo de vida do Hardware (HLH): antes de compilação 1902, era necessário definir diretiva de grupo **computador configuração Windows Settings\Local \ opções** para **Enviar LM e NTLM – utilizar a segurança de sessão de NTLMv2 se negociado**. Desde compilação 1902, tem de deixar-o como **não definido** ou defina-o como **apenas a resposta de enviar NTLMv2** (que é o valor predefinido). Caso contrário, não será possível estabelecer uma sessão remota do PowerShell e verá uma **o acesso é negado** erro:

   ```shell
   PS C:\Users\Administrator> $session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint  -Credential $cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the 
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

### <a name="networking"></a>Redes  

<!-- 3239127 - IS, ASDK -->
- No portal do Azure Stack, quando altera um endereço IP estático para uma configuração de IP que está vinculado a um adaptador de rede anexado a uma instância VM, verá uma mensagem de aviso que indica 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    Pode ignorar esta mensagem; o endereço IP será alterado, mesmo que a instância VM não é reinicializado.

<!-- 3632798 - IS, ASDK -->
- No portal, se adicionar uma regra de segurança de entrada e selecione **etiquetas de serviço** como a origem, várias opções são apresentadas no **marca de origem** lista que não estão disponíveis para o Azure Stack. As únicas opções que são válidas no Azure Stack são os seguintes:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  As outras opções não são suportadas como etiquetas de origem no Azure Stack. Da mesma forma, se adicionar uma regra de segurança de saída e selecione **etiquetas de serviço** como o destino, a mesma lista de opções para **marca de origem** é apresentado. As opções apenas válidas são os mesmos que para **marca de origem**, conforme descrito na lista anterior.

- Grupos de segurança de rede (NSGs) não funcionam no Azure Stack estar na mesma forma como global. No Azure, pode definir várias portas numa regra NSG (com o portal, PowerShell e modelos do Resource Manager). No Azure Stack, no entanto, não é possível definir várias portas numa regra NSG através do portal. Para contornar este problema, utilize um modelo do Resource Manager ou o PowerShell para definir estas regras adicionais.

<!-- 3203799 - IS, ASDK -->
- O Azure Stack não suporta a ligação a mais de 4 Interfaces de rede (NICs) a uma instância VM de hoje em dia, independentemente do tamanho de instância.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>Serviço de Aplicações

<!-- 2352906 - IS ASDK --> 
- Tem de registar o fornecedor de recursos de armazenamento antes de criar a sua primeira função do Azure na subscrição.

<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog

- A configuração de syslog não é persistente através de um ciclo de atualização, fazendo com que o cliente de syslog perder a respetiva configuração e as mensagens syslog para parar a ser reencaminhados. Este problema aplica-se a todas as versões do Azure Stack desde os de disponibilidade geral do cliente de syslog (1809). Para contornar este problema, reconfigure o cliente de syslog depois de aplicar uma atualização do Azure Stack.

## <a name="download-the-update"></a>Transferir a atualização

Pode transferir o pacote de atualização do Azure Stack 1903 partir [aqui](https://aka.ms/azurestackupdatedownload). 

Em cenários de ligação apenas, implementações do Azure Stack verificar periodicamente um ponto de extremidade seguro e notificam automaticamente se uma atualização está disponível para a sua nuvem. Para obter mais informações, consulte [gerir atualizações para o Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral da gestão de atualizações no Azure Stack, veja [gerir atualizações na descrição geral do Azure Stack](azure-stack-updates.md).  
- Para obter mais informações sobre como aplicar as atualizações com o Azure Stack, veja [aplicar atualizações no Azure Stack](azure-stack-apply-updates.md).
- Para rever a política de manutenção para sistemas integrados do Azure Stack e o que deve fazer para manter seu sistema num estado suportado, consulte [política de manutenção do Azure Stack](azure-stack-servicing-policy.md).  
- Para utilizar o ponto final com privilégios (PEP) para monitorizar e retomar as atualizações, consulte [monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios](azure-stack-monitor-update.md).  

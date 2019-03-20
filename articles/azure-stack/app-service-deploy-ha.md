---
title: Implementar o serviço de aplicações do Azure Stack numa configuração de elevada disponibilidade | Documentos da Microsoft
description: Saiba como implementar o serviço de aplicações no Azure Stack com uma configuração de elevada disponibilidade.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/13/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 50e5272d92ad333e70f65173cf024d165dc7501c
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58102048"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Implementar o serviço de aplicações numa configuração de elevada disponibilidade

Este artigo mostra como utilizar itens do marketplace do Azure Stack para implementar o serviço de aplicações para o Azure Stack numa configuração de elevada disponibilidade. Além de itens do marketplace disponível, esta solução também utiliza a [appservice-partilha de ficheiros-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modelo de início rápido do Azure Stack. Este modelo automatiza a criação de uma infraestrutura de elevada disponibilidade para alojar o fornecedor de recursos do serviço de aplicações. Serviço de aplicações é instalado nesta infraestrutura VM de elevada disponibilidade. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Implementar a infraestrutura de serviço de aplicações de elevada disponibilidade VMs
O [appservice-partilha de ficheiros-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modelo de início rápido do Azure Stack simplifica a implementação do serviço de aplicações numa configuração de elevada disponibilidade. Devem ser implementado na subscrição do fornecedor predefinido. 

Quando usado para criar um recurso personalizado no Azure Stack, o modelo cria:
- Uma rede virtual e sub-redes necessárias.
- Grupos de segurança de rede para o servidor de ficheiros, SQL Server e sub-redes do Active Directory Domain Services (AD DS).
- Contas de armazenamento para discos VM e o testemunho de nuvem do cluster.
- Um balanceador de carga interno para VMs de SQL com o IP privado associado para o serviço de escuta do SQL AlwaysOn.
- Três conjuntos de disponibilidade para o AD DS, o cluster de servidor de ficheiros e o cluster do SQL.
- Cluster de SQL de dois nós.
- Cluster de servidores de ficheiros de dois nós.
- Dois controladores de domínio.

### <a name="required-azure-stack-marketplace-items"></a>Itens de marketplace necessários do Azure Stack
Antes de utilizar este modelo, certifique-se de que o seguinte procedimento [itens do marketplace do Azure Stack](azure-stack-marketplace-azure-items.md) estão disponíveis na sua instância do Azure Stack:

- Imagem do Windows Server 2016 Datacenter Core (para AD DS e servidores de ficheiros VMs)
- O SQL Server 2016 SP2 no Windows Server 2016 (Enterprise)
- Extensão de IaaS mais recente do SQL 
- Extensão de Desired State Configuration do PowerShell mais recente 

> [!TIP]
> Revisão [o arquivo Leiame do modelo](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) no GitHub para obter mais informações sobre os requisitos de modelo e os valores predefinidos. 

### <a name="deploy-the-app-service-infrastructure"></a>Implementar a infraestrutura do serviço de aplicações
Utilize os passos nesta secção para criar uma implementação personalizada com o **appservice-partilha de ficheiros-sqlserver-ha** modelo de início rápido do Azure Stack.

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Selecione **\+** **criar um recurso** > **personalizado**e, em seguida, **implementação do modelo**.

   ![Implementação de modelo personalizado](media/app-service-deploy-ha/1.png)


3. Sobre o **implementação personalizada** painel, selecione **Editar modelo** > **modelo de início rápido** e, em seguida, utilize a lista de lista pendente de disponíveis modelos personalizados para Selecione o **appservice-partilha de ficheiros-sqlserver-ha** modelo, clique em **OK**e, em seguida **guardar**.

   ![Selecione o modelo de serviço de aplicações-partilha de ficheiros-sqlserver-ha início rápido](media/app-service-deploy-ha/2.png)

4. Sobre o **implementação personalizada** painel, selecione **Editar parâmetros** e desloque-se para baixo para rever os valores predefinidos do modelo. Modifique estes valores conforme necessário para fornecer todas as informações de parâmetro necessário e, em seguida, clique em **OK**.<br><br> No mínimo, fornece palavras-passe complexas para os parâmetros ADMINPASSWORD, FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD e SQLLOGINPASSWORD.
    
   ![Editar parâmetros de implementação personalizada](media/app-service-deploy-ha/3.png)

5. Na **implementação personalizada** painel, certifique-se **subscrição do fornecedor predefinido** está selecionado como a subscrição que pretende utilizar e, em seguida, crie um novo grupo de recursos ou selecione um grupo de recursos existente, para o personalizado implementação.<br><br> Em seguida, selecione a localização do grupo de recursos (**local** para instalações de ASDK) e, em seguida, clique em **criar**. As definições de implementação personalizado vão ser validadas antes de inicia a implementação do modelo.

    ![Criar implementação personalizada](media/app-service-deploy-ha/4.png)

6. No portal de administração, selecione **grupos de recursos** e, em seguida, o nome do grupo de recursos que criou para a implementação personalizada (**app-service-ha** neste exemplo). Ver o estado da implementação para garantir que todas as implementações foram concluídas com êxito.

   > [!NOTE]
   > A implementação do modelo irá demorar cerca de uma hora para concluir.

   [![](media/app-service-deploy-ha/5-sm.png "Rever o estado de implementação do modelo")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Modelo do registo de saídas
Depois do modelo de implementação for concluída com êxito, a implementação do modelo saídas de registo. Terá de fornecer estas informações ao executar o instalador do serviço de aplicações. 

Certifique-se de que Registre cada um desses valores de saída:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Siga estes passos para detetar os valores de saída do modelo:

1. 
   [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. No portal de administração, selecione **grupos de recursos** e, em seguida, o nome do grupo de recursos que criou para a implementação personalizada (**app-service-ha** neste exemplo). 

3. Clique em **implementações** e selecione **Template**.

    ![Implementação de Template](media/app-service-deploy-ha/6.png)

4. Depois de selecionar o **Template** implementação, selecione **saídas** e gravar o resultado do parâmetro de modelo. Esta informação será necessária quando implementar o serviço de aplicações.

    ![Saída de parâmetro](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Implementar o serviço de aplicações numa configuração de elevada disponibilidade
Siga os passos nesta secção para implementar o serviço de aplicações para o Azure Stack numa configuração de elevada disponibilidade com base na [appservice-partilha de ficheiros-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) modelo de início rápido do Azure Stack. 

Depois de instalar o fornecedor de recursos do serviço de aplicações, pode incluí-lo em seus planos e ofertas. Os utilizadores, em seguida, podem subscrever para obter o serviço e comece a criar aplicações.

> [!IMPORTANT]
> Antes de executar o instalador de fornecedor de recursos, certifique-se de que leu as notas de versão, que acompanham cada versão do serviço de aplicações, para saber mais sobre novas funcionalidades, correções e os problemas conhecidos que podem afetar a sua implementação.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder executar o instalador do serviço de aplicações, vários passos são necessários, tal como descrito no [antes de começar com o serviço de aplicações no artigo do Azure Stack](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Nem todos os passos descritos no antes de iniciar o artigo são necessárias porque o recurso de modelo configura a infra-estrutura de VMs para. 

- [Transferir os scripts de instalador e auxiliar do serviço de aplicações](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Transfira a extensão de script personalizado mais recentes para o mercado do Azure Stack](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Gerar os certificados necessários](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Crie a aplicação de ID baseada no provedor de identidade que escolheu para o Azure Stack. Um aplicativo de ID podem ser feito por qualquer [do Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) ou [serviços de Federação do Active Directory](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) e registe o ID da aplicação.
- Certifique-se de que adicionou a imagem do Windows Server 2016 Datacenter para o mercado do Azure Stack. Isto é necessário para a instalação do serviço de aplicações.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Implementação do serviço de aplicações numa configuração de elevada disponibilidade
Instalar o fornecedor de recursos do serviço de aplicações, pelo menos, leva uma hora. O período de tempo necessário depende da função quantas instâncias implementar. Durante a implantação, o instalador executa as seguintes tarefas:

- Crie um contentor de BLOBs na conta de armazenamento do Azure Stack especificada.
- Crie uma zona DNS e as entradas para o serviço de aplicações.
- Registe o fornecedor de recursos do serviço de aplicações.
- Registre-se os itens de galeria do serviço de aplicações.

Para implementar o fornecedor de recursos do serviço de aplicações, siga estes passos:

1. Execute o instalador do serviço de aplicações anteriormente transferido (**appservice.exe**) como um administrador a partir de um computador que pode aceder ao Azure Stack administrador do Azure Resource ponto final de gestão.

2. Selecione **implementar o serviço de aplicações ou Atualize para a versão mais recente**.

    ![Implementar ou atualizar](media/app-service-deploy-ha/01.png)

3. Aceite os termos de licenciamento da Microsoft e clique em **seguinte**.

    ![Termos de licenciamento da Microsoft](media/app-service-deploy-ha/02.png)

4. Aceite os termos de licenciamento de terceiros e clique em **seguinte**.

    ![Termos de licenciamento de não-Microsoft](media/app-service-deploy-ha/03.png)

5. Fornece o serviço de aplicações a configuração de ponto final de cloud para o seu ambiente do Azure Stack.

    ![Configuração do ponto final do serviço de aplicações na cloud](media/app-service-deploy-ha/04.png)

6. **Ligar** à subscrição do Azure Stack para ser utilizada para a instalação e escolha a localização. 

    ![Ligar à subscrição do Azure Stack](media/app-service-deploy-ha/05.png)

7. Selecione **utilizar a VNet existente e sub-redes** e o **nome do grupo de recursos** para o grupo de recursos utilizado para implementar o modelo de elevada disponibilidade.<br><br>Em seguida, selecione a rede virtual criada como parte da implementação do modelo e, em seguida, selecione as sub-redes da função adequada entre as opções na lista pendente. 

    ![Seleção de Vnet](media/app-service-deploy-ha/06.png)

8. Fornece que o modelo gravado anteriormente produz informações para o caminho da partilha de ficheiros e os parâmetros de proprietário de partilha de ficheiros. Quando terminar, clique em **seguinte**.

    ![Informações de saída de partilha de ficheiros](media/app-service-deploy-ha/07.png)

9. Uma vez que a máquina a ser utilizada para instalar o serviço de aplicações não estiver localizada na mesma VNet como o servidor de ficheiros que está a ser utilizado para alojar a partilha de ficheiros do serviço de aplicações, não será capaz de resolver o nome. Este comportamento está previsto.<br><br>Certifique-se de que as informações de inserido para as informações de contas e o caminho UNC de partilha de ficheiros estão corretas e prima **Sim** da caixa de diálogo de alerta para continuar a instalação do serviço de aplicações.

    ![Caixa de diálogo de erro esperados](media/app-service-deploy-ha/08.png)

10. Forneça o ID da aplicação de identidade e o caminho e palavras-passe para os certificados de identidade e clique em **seguinte**:
    - Certificado Identity application (no formato **sso.appservice.local.azurestack.external.pfx**)
    - Certificado de raiz do Azure Resource Manager (**AzureStackCertificationAuthority.cer**)

    ![O certificado de aplicação de ID e o certificado de raiz](media/app-service-deploy-ha/008.png)

10. Em seguida, forneça as restantes informações necessárias para os seguintes certificados e clique em **seguinte**:
    - Certificado SSL do Azure Stack predefinido (no formato **_.appservice.local.azurestack.external.pfx**)
    - Certificado SSL de API (no formato **api.appservice.local.azurestack.external.pfx**)
    - Certificado do publicador (na forma de **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certificados de configuração adicionais](media/app-service-deploy-ha/09.png)

11. Forneça as informações de ligação do SQL Server com as informações de ligação do SQL Server de saídas de implementação do modelo de elevada disponibilidade:

    ![Informações de ligação do SQL Server](media/app-service-deploy-ha/10.png)

12. Uma vez que a máquina a ser utilizada para instalar o serviço de aplicações não estiver localizada na mesma VNet como o SQL server que está a ser utilizado para alojar as bases de dados do serviço de aplicações, não será capaz de resolver o nome.  Este comportamento está previsto.<br><br>Certifique-se de que as informações de inserido para as informações de nome e as contas do SQL Server estão corretas e prima **Sim** para continuar a instalação do serviço de aplicações. Clique em **Seguinte**.

    ![Informações de ligação do SQL Server](media/app-service-deploy-ha/11.png)

13. Aceite os valores de configuração de função predefinidos ou alterar para os valores recomendados e clique em **seguinte**.<br><br>Recomendamos que os valores predefinidos para as instâncias de função de infraestrutura do serviço de aplicações seja alterada o da seguinte forma para configurações de elevada disponibilidade:

    |Função|Predefinição|Recomendação de elevada disponibilidade|
    |-----|-----|-----|
    |Função de controlador|2|2|
    |Função de Gestão|1|3|
    |Função de publicador|1|3|
    |Função Front-End|1|3|
    |Função de trabalhador partilhada|1|10|
    |     |     |     |

    ![Valores de instância de função de infraestrutura](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Alterar os valores de defualt às recomendada no tutoral que aumenta os requisitos de hardware para instalar o serviço de aplicações. Um total de 26 de núcleos e 46,592 MB de RAM é necessária para suportar as VMs de 21 recomendadas em vez dos núcleos de 18 defualt e 32,256 MB de RAM para 15 VMs.

14. Selecione a imagem de plataforma a utilizar para instalar as VMs de infraestrutura do serviço de aplicações e clique em **seguinte**:

    ![Seleção de imagem de plataforma](media/app-service-deploy-ha/13.png)

15. Fornecer informações de credenciais de função de infraestrutura para ser utilizado e clique em serviço de aplicações **seguinte**:

    ![Credenciais de função de infraestrutura](media/app-service-deploy-ha/14.png)

16. Reveja as informações para ser utilizado para implementar o serviço de aplicações e clique em **seguinte** para iniciar a implantação. 

    ![Resumo da instalação de revisão](media/app-service-deploy-ha/15.png)

17. Reveja o progresso da implementação de serviço de aplicações. Esta ação pode demorar mais de uma hora, dependendo da configuração de implementação específico e o hardware. Depois do instalador for concluída com êxito, selecione **saída**.

    ![Configuração concluída](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Passos Seguintes

[Aumentar horizontalmente o serviço de aplicações](azure-stack-app-service-add-worker-roles.md). Poderá ter de adicionar trabalhos de função do serviço de aplicações infra-estrutura adicionais para atender à demanda de aplicativo esperado no seu ambiente. Por predefinição, o serviço de aplicações no Azure Stack suporta escalões de worker gratuito e partilhado. Para adicionar outros escalões de worker, terá de adicionar mais funções de trabalho.

[Configurar origens de implementação](azure-stack-app-service-configure-deployment-sources.md). Configuração adicional é necessária para suportar a implementação de demanda de vários fornecedores de controle de fonte como GitHub, BitBucket, OneDrive e DropBox.

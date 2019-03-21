---
title: 'Implemente serviços de aplicações: O Azure Stack | Documentos da Microsoft'
description: Orientação detalhada para implementar o serviço de aplicações no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: d66254cdad596e3b10482b2c937326162e2e075d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57886835"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Adicionar um fornecedor de recursos do serviço de aplicações para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Utilize as orientações neste artigo para implementar o serviço de aplicações no Azure Stack.

> [!IMPORTANT]  
> Aplicar a atualização de 1901 seu sistema integrado do Azure Stack ou implementar a mais recente do Azure Stack Development Kit (ASDK) antes de implementar 1.5 de serviço de aplicações do Azure.

Pode dar a capacidade de criar aplicações de API web e os seus utilizadores. Para permitir aos utilizadores criar esses aplicativos, terá de:

 - Adicionar a [fornecedor de recursos do serviço de aplicações](azure-stack-app-service-overview.md) à sua implementação do Azure Stack através dos passos descritos neste artigo.
 - Depois de instalar o fornecedor de recursos do serviço de aplicações, pode incluí-lo em seus planos e ofertas. Os utilizadores, em seguida, podem subscrever para obter o serviço e comece a criar aplicações.

> [!IMPORTANT]  
> Antes de executar o instalador de fornecedor de recursos, certifique-se de que seguiu a documentação de orientação [antes de começar](azure-stack-app-service-before-you-get-started.md) e li a [notas de versão](azure-stack-app-service-release-notes-update-five.md), que acompanham a versão 1.5, para saber mais sobre o novo funcionalidade, correções e os problemas conhecidos que podem afetar a sua implementação.

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador de fornecedor de recursos do serviço de aplicações

Instalar o fornecedor de recursos do serviço de aplicações, pelo menos, leva uma hora. O período de tempo necessário depende da função quantas instâncias implementar. Durante a implantação, o instalador executa as seguintes tarefas:

- Crie um contentor de BLOBs na conta de armazenamento do Azure Stack especificada.
- Crie uma zona DNS e as entradas para o serviço de aplicações.
- Registe o fornecedor de recursos do serviço de aplicações.
- Registre-se os itens de galeria do serviço de aplicações.

  > [!IMPORTANT]
  > Antes de implementar o fornecedor de recursos, reveja as notas de versão para saber mais sobre novas funcionalidades, correções e os problemas conhecidos que podem afetar a sua implementação.

Para implementar o fornecedor de recursos do serviço de aplicações, siga estes passos:

1. Execute appservice.exe como um administrador de um computador que pode aceder ao Azure Stack administrador do Azure Resource ponto final de gestão.

2. Selecione **implementar o serviço de aplicações ou Atualize para a versão mais recente**.

    ![Instalador do serviço de aplicações][1]

3. Reveja e aceite os termos de licença de Software Microsoft e, em seguida, selecione **seguinte**.

4. Reveja e aceite os termos de licença de terceiros e, em seguida, selecione **seguinte**.

5. Certifique-se de que as informações de configuração do serviço de aplicações na cloud estão corretas. Se usou as configurações padrão durante a implementação do Azure Stack Development Kit (ASDK), pode aceitar os valores predefinidos. No entanto, se as opções personalizado quando implementado o ASDK ou estiver a implementar num sistema integrado do Azure Stack, tem de editar os valores nesta janela para refletir as diferenças.

   Por exemplo, se usar o mycloud.com de sufixo de domínio, o ponto de final do Gestor de recursos do Azure de inquilino do Azure Stack tem de alterar para gestão. &lt;região&gt;. mycloud.com. Reveja estas definições e, em seguida, selecione **seguinte** para guardar as definições.

   ![Instalador do serviço de aplicações][2]

6. Na página seguinte do instalador do serviço de aplicações, siga estes passos:

    a. Selecione **Connect** junto a **subscrições do Azure Stack**.

   - Se estiver a utilizar o Azure Active Directory (Azure AD), introduza a conta de administrador do Azure AD e a palavra-passe que forneceu quando implementou o Azure Stack. Selecione **iniciar sessão**.
   - Se estiver a utilizar os serviços de Federação do Active Directory (AD FS), forneça a sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Introduza a palavra-passe e, em seguida, selecione **sessão**.

   b. Na **subscrições do Azure Stack**, selecione a **subscrição do fornecedor predefinido**.

     > [!IMPORTANT]  
     > Serviço de aplicações **tem** ser implementada para o **subscrição do fornecedor predefinido**.

   c. Na **localizações do Azure Stack**, selecione a localização que corresponde à região que está a implementar. Por exemplo, seleccione **local** se sua a implementar o Development Kit do Azure Stack.

    ![Instalador do serviço de aplicações][3]

7. Agora pode implementar numa rede virtual existente que configurou [seguindo estes passos](azure-stack-app-service-before-you-get-started.md#virtual-network), ou permitir que o instalador do serviço de aplicações criar uma nova rede virtual e sub-redes. Para criar uma VNet, siga estes passos:

   a. Selecione **criar VNet com configurações padrão**, aceite as predefinições e, em seguida, selecione **próxima**.

   b. Em alternativa, selecione **utilizar a VNet existente e sub-redes**. Conclua as seguintes ações:

     - Selecione o **grupo de recursos** que contém a sua rede Virtual.
     - Escolha o **rede Virtual** nome que pretende implementar.
     - Selecione o correto **sub-rede** valores para cada um das sub-redes de função necessários.
     - Selecione **Seguinte**.

   ![Instalador do serviço de aplicações][4]

8. Introduza as informações para a partilha de ficheiros e, em seguida, selecione **seguinte**. O endereço da partilha de ficheiros tem de utilizar o nome completamente qualificado domínio (FQDN) ou o endereço IP do seu servidor de ficheiros. Por exemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.  Se estiver a utilizar um servidor de ficheiros que é associado a um domínio, tem de fornecer o nome de utilizador completo, incluindo o domínio, por exemplo, myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >O instalador tentará testar a conectividade à partilha de ficheiros antes de continuar. No entanto, se estiver a implementar uma rede virtual existente, o teste de ligação poderá falhar. É-lhe fornecido um aviso e uma linha de comandos para continuar. Se as informações da partilha de ficheiros estiverem corretas, continue a implantação.

   ![Instalador do serviço de aplicações][7]

9. Na página seguinte do instalador do serviço de aplicações, siga estes passos:

   a. Na **ID da Identity Application** , introduza o GUID para a aplicação estiver a utilizar para a identidade (a partir do Azure AD).

   b. Na **o ficheiro de certificado de aplicação de identidade** caixa, introduza (ou navegar até) a localização do ficheiro de certificado.

   c. Na **palavra-passe de certificado de aplicação de identidade** , introduza a palavra-passe para o certificado. Esta palavra-passe é aquela que anotou quando utilizou o script para criar os certificados.

   d. Na **ficheiro de certificado de raiz do Azure Resource Manager** caixa, introduza (ou navegar até) a localização do ficheiro de certificado.

   e. Selecione **Seguinte**.

   ![Instalador do serviço de aplicações][9]

10. Para cada uma das caixas de ficheiro de certificado de três, selecione **procurar** e navegue para o ficheiro de certificado adequado. Tem de fornecer a palavra-passe para cada certificado. Estes certificados são aqueles que criou no [passo de certificados necessários criar](azure-stack-app-service-before-you-get-started.md#get-certificates). Selecione **seguinte** após introduzir todas as informações.

    | Box | Exemplo de nome de ficheiro de certificado |
    | --- | --- |
    | **Ficheiro de certificado SSL do serviço de aplicações predefinido** | \_.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado SSL do serviço de aplicações API** | api.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado de SSL de publicador do serviço de aplicações** | ftp.appservice.local.AzureStack.external.pfx |

    Se utilizou um sufixo de domínio diferente ao criar os certificados, os nomes de ficheiro de certificado não utilizem *local. AzureStack.external*. Em vez disso, utilize as informações de domínio personalizado.

    ![Instalador do serviço de aplicações][10]

11. Introduza os detalhes do SQL Server para a instância de servidor utilizada para alojar as bases de dados do fornecedor de recursos do serviço de aplicações e, em seguida, selecione **seguinte**. O instalador valida as propriedades de ligação de SQL.<br><br>O instalador do serviço de aplicações tenta testar a conectividade ao SQL Server antes de continuar. Se estiver a implementar uma rede virtual existente, o teste de ligação poderá falhar. É-lhe fornecido um aviso e uma linha de comandos para continuar. Se as informações do SQL Server estão corretas, continue a implantação.

    ![Instalador do serviço de aplicações][11]

12. Reveja as opções de SKU e a instância de função. As predefinições preenchido com o número mínimo de instâncias e o SKU mínimo para cada função numa implantação ASDK. Para ajudar a planear a implementação, é fornecido um resumo dos requisitos de memória e vCPU. Depois de fazer as seleções, selecione **seguinte**.

    >[!NOTE]
    >Para implementações de produção, seguir as orientações no [planeamento de capacidade de funções de servidor do App Service do Azure no Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Função | Instâncias mínimas | SKU mínimo | Notas |
    | --- | --- | --- | --- |
    | Controlador | 1 | Standard_A2 - (2 vCPU, 3584 MB) | Gerencia e mantém o estado de funcionamento da cloud do serviço de aplicações. |
    | Gestão | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Gere os pontos finais de API e aplicação de serviço do Azure Resource Manager, portais extensões (administração, inquilino, o portal de funções) e o serviço de dados. Para suportar a ativação pós-falha, aumento as instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica conteúdo através de implementação de FTP e web. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha os pedidos para aplicativos de serviço de aplicações. |
    | Trabalho partilhado | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Web de anfitriões ou aplicações de API e aplicações de funções do Azure. Pode querer adicionar mais instâncias. Como um operador, pode definir a sua oferta e escolha qualquer escalão SKU. Os escalões tem de ter um mínimo de um vCPU. |

    ![Instalador do serviço de aplicações][13]

    >[!NOTE]
    >**Windows Server 2016 Core não é uma imagem de plataforma suportada para utilização com o serviço de aplicações do Azure no Azure Stack.  Não utilize imagens de avaliação para implementações de produção.**

13. Na **selecione a imagem de plataforma** caixa, escolha sua imagem de máquina virtual de implantação do Windows Server 2016 a partir das imagens disponíveis no fornecedor de recursos de computação para a cloud de serviço de aplicações. Selecione **Seguinte**.

14. Na página seguinte do instalador do serviço de aplicações, siga estes passos:

     a. Introduza o nome de utilizador de administrador de máquina virtual de função de trabalho e a palavra-passe.

     b. Introduza o nome de utilizador de administrador de máquina virtual de outras funções e a palavra-passe.

     c. Selecione **Seguinte**.

    ![Instalador do serviço de aplicações][15]

15. Na página de resumida do instalador do serviço de aplicações, siga estes passos:

    a. Certifique-se seleções que fez. Para fazer alterações, utilize o **Previous** botões para visitar as páginas anteriores.

    b. Se as configurações estão corretas, selecione a caixa de verificação.

    c. Para iniciar a implementação, selecione **seguinte**.

    ![Instalador do serviço de aplicações][16]

16. Na página seguinte do instalador do serviço de aplicações, siga estes passos:

    a. Controle o progresso da instalação. Serviço de aplicações no Azure Stack demora cerca de 60 minutos para implementar com base nas seleções padrão.

    b. Depois do instalador for concluída com êxito, selecione **saída**.

    ![Instalador do serviço de aplicações][17]

## <a name="post-deployment-steps"></a>Passos de pós-implementação

> [!IMPORTANT]  
> Se forneceu a RP de serviço de aplicações com uma sempre na instância do SQL tem [adicionar as bases de dados appservice_hosting e appservice_metering a um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) e sincronizar as bases de dados para evitar qualquer perda de serviço do evento de uma ativação pós-falha de base de dados.

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicações numa instalação do Azure Stack

1. No portal de administração do Azure Stack, aceda a **administração - serviço de aplicações**.

2. Na descrição geral, em estado, verifique que o **Status** apresenta **todas as funções estão prontas**.

    ![Gestão de serviço de aplicações](media/azure-stack-app-service-deploy/image12.png)

    Se estiver a implementar uma rede virtual existente e utilizar um endereço IP interno para ligar ao seu servidor de ficheiros, tem de adicionar uma regra de segurança de saída. Esta regra permite o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB.  Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:

    - Origem: Qualquer
    - Intervalo de portas de origem: *
    - Destino: Endereços IP
    - Intervalo de endereços IP de destino: Intervalo de IPs para o servidor de ficheiros
    - Intervalo de portas de destino: 445
    - Protocolo: TCP
    - Ação: Permitir
    - Prioridade: 700
    - Nome: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Experimente o serviço de aplicações no Azure Stack

Depois de implementar e registar o fornecedor de recursos do serviço de aplicações, testá-lo para certificar-se de que os utilizadores podem implementar aplicações web e API.

>[!NOTE]
>Tem de criar uma oferta que tem o espaço de nomes Microsoft. Web no plano. Também precisa de uma subscrição de inquilino que assina a oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
>*Tem* tem uma subscrição de inquilino para criar aplicativos que usam o serviço de aplicações no Azure Stack. As tarefas apenas que um administrador de serviço pode realizar no portal de administração estão relacionados com a administração de fornecedor de recursos do serviço de aplicações. Isso inclui adicionar capacidade, a configuração de origens de implementação e a adição de escalões de Worker e SKUs.
>
>Para criar a web, API e o Azure funciona aplicações, tem de utilizar o portal de inquilinos e ter uma subscrição de inquilino.
>

Para criar uma aplicação web de teste, siga estes passos:

1. No portal de utilizador do Azure Stack, selecione **+ criar um recurso** > **Web + móvel** > **aplicação Web**.

2. Sob **aplicação Web**, introduza um nome na **aplicação Web**.

3. Sob **grupo de recursos**, selecione **New**. Introduza um nome para o **grupo de recursos**.

4. Selecione **plano do serviço de aplicações/localização** > **criar novo**.

5. Sob **plano do App Service**, introduza um nome para o **plano do App Service**.

6. Selecione **escalão de preço** > **gratuito partilhado** ou **partilhado partilhado** > **selecione**  >  **OK** > **criar**.

7. É apresentado um mosaico da aplicação web nova no dashboard. Selecione o mosaico.

8. No **aplicação Web**, selecione **procurar** para ver o Web site predefinido para esta aplicação.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementar um site WordPress, DNN ou Django (opcional)

1. No portal de inquilino do Azure Stack, selecione **+**, vá para o Azure Marketplace, implemente um Web site Django e, em seguida, aguarde pela conclusão da implementação. A plataforma de web Django utiliza um ficheiro com base no sistema base de dados do. Ele não requer quaisquer fornecedores de recursos adicionais, tais como SQL ou MySQL.

2. Se implementou também um provedor de recursos do MySQL, pode implementar um Web site WordPress no Marketplace. Quando lhe for pedido para os parâmetros de base de dados, introduza o nome de utilizador como *User1\@servidor1*, com o nome de utilizador e o nome do servidor da sua preferência.

3. Se implementou também um provedor de recursos do SQL Server, pode implementar um Web site DNN do Marketplace. Quando lhe for pedido para os parâmetros de base de dados, escolha uma base de dados no computador que executa o SQL Server que está ligada ao seu fornecedor de recursos.

## <a name="next-steps"></a>Passos Seguintes

Também pode tentar outros [plataforma como um serviço de serviço (PaaS)](azure-stack-tools-paas-services.md).

 - [Fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
 - [Fornecedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png

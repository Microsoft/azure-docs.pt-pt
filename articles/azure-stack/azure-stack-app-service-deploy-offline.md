---
title: Implementar o serviço de aplicações num ambiente offline no Azure Stack | Documentos da Microsoft
description: Documentação de orientação detalhada sobre como implementar o serviço de aplicações num ambiente do Azure Stack desligado protegido pelo AD FS.
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
ms.openlocfilehash: b6950e3445f2320f2e3a45f55726befd7077119a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835914"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Adicionar um fornecedor de recursos do serviço de aplicações para um ambiente desligado do Azure Stack protegido pelo AD FS

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!IMPORTANT]
> Aplicar a atualização de 1901 seu sistema integrado do Azure Stack ou implementar o development kit do Azure Stack mais recentes antes de implementar 1.5 de serviço de aplicações do Azure.

Ao seguir as instruções neste artigo, pode instalar o [fornecedor de recursos do serviço de aplicações](azure-stack-app-service-overview.md) para um ambiente do Azure Stack que é:

- não ligado à Internet
- protegida por serviços de Federação do Active Directory (AD FS).

> [!IMPORTANT]  
> Antes de executar o instalador de fornecedor de recursos, certifique-se de que seguiu a documentação de orientação [antes de começar](azure-stack-app-service-before-you-get-started.md) e li a [notas de versão](azure-stack-app-service-release-notes-update-five.md) que acompanham o 1.5 versão conhecer novas funcionalidade, correções e os problemas conhecidos que podem afetar a sua implementação.

Para adicionar o fornecedor de recursos do serviço de aplicações para a sua implementação do Azure Stack offline, tem de concluir estas tarefas de nível superior:

1. Concluir o [passos de pré-requisitos](azure-stack-app-service-before-you-get-started.md) (como comprar certificados, o que pode demorar alguns dias para receber).
2. [Transfira e extraia os ficheiros de instalação e auxiliar](azure-stack-app-service-before-you-get-started.md) a um computador ligado à Internet.
3. Crie um pacote de instalação offline.
4. Execute o ficheiro de instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Criar um pacote de instalação offline

Para implementar o serviço de aplicações num ambiente desligado, primeiro tem de criar um pacote de instalação offline num computador que está ligado à Internet.

1. Execute o instalador de AppService.exe num computador que está ligado à Internet.

2. Clique em **avançadas** > **criar pacote de instalação offline**.

    ![Instalador do serviço de aplicações][1]

3. O instalador do serviço de aplicações cria um pacote de instalação offline e apresenta o caminho para o mesmo. Pode clicar em **Abrir pasta** para abrir a pasta no Explorador de ficheiros.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copie o instalador (AppService.exe) e o pacote de instalação offline à sua máquina de anfitrião do Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Concluir a instalação offline do serviço de aplicações no Azure Stack

1. Execute appservice.exe como um administrador de um computador que pode alcançar o ponto de final de gestão de recursos do Azure de administrador do Azure Stack.

2. Clique em **avançadas** > **concluir instalação offline**.

    ![Instalador do serviço de aplicações][2]

3. Navegue até à localização do pacote de instalação offline que criou anteriormente e, em seguida, clique em **seguinte**.

    ![Instalador do serviço de aplicações](media/azure-stack-app-service-deploy-offline/image04.png)

4. Reveja e aceite os termos de licença de Software Microsoft e, em seguida, clique em **seguinte**.

5. Reveja e aceite os termos de licença de terceiros e, em seguida, clique em **seguinte**.

6. Certifique-se de que as informações de configuração do serviço de aplicações na cloud estão corretas. Se usou as configurações padrão durante a implementação do Development Kit do Azure Stack, pode aceitar os valores predefinidos aqui. No entanto, se as opções personalizado quando implementado o Azure Stack ou estiver a implementar num sistema integrado, tem de editar os valores nesta janela para refletir a alteração. Por exemplo, se usar o mycloud.com de sufixo de domínio, o ponto de final do Gestor de recursos do Azure de inquilino do Azure Stack tem de alterar para gestão. <region>. mycloud.com. Depois de confirmar suas informações, clique em **seguinte**.

    ![Instalador do serviço de aplicações][3]

7. Na página seguinte:
   1. Clique nas **Connect** junto aos **subscrições do Azure Stack** caixa.
      - Forneça a sua conta de administrador. Por exemplo, cloudadmin@azurestack.local. Introduza a palavra-passe e clique em **sessão**.
   2. Na **subscrições do Azure Stack** caixa, selecione a **subscrição do fornecedor predefinido**.
    
      > [!NOTE]
      > Serviço de aplicações só pode ser implementado para o **subscrição do fornecedor predefinido**.
      >
    
   3. Na **localizações do Azure Stack** caixa, selecione a localização que corresponde à região que está a implementar. Por exemplo, seleccione **local** se sua a implementar o Development Kit do Azure Stack.
   4. Clique em **Seguinte**.

      ![Instalador do serviço de aplicações][4]

8. Agora tem a opção de implementar numa rede Virtual existente, conforme configurado através dos passos [aqui](azure-stack-app-service-before-you-get-started.md#virtual-network), ou permitir que o instalador do serviço de aplicações criar uma rede Virtual e sub-redes associadas.
   1. Selecione **criar VNet com configurações padrão**, aceite as predefinições e, em seguida, clique em **próxima**, ou;
   2. Selecione **utilizar a VNet existente e sub-redes**.
       1. Selecione o **grupo de recursos** que contém a sua rede Virtual;
       2. Escolha o correto **rede Virtual** nome que pretende implementar numa;
       3. Selecione o correto **sub-rede** valores para cada uma das sub-redes de função necessários;
       4. Clique em **Seguinte**

      ![Instalador do serviço de aplicações][5]

9. Introduza as informações para a partilha de ficheiros e, em seguida, clique em **seguinte**. O endereço da partilha de ficheiros tem de utilizar o nome de domínio completamente qualificado ou endereço IP do seu servidor de ficheiros. Por exemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.  Se estiver a utilizar um servidor de ficheiros que é associado a um domínio, tem de fornecer o nome de utilizador completo, incluindo o domínio, por exemplo, myfileserverdomain\FileShareOwner.

    > [!NOTE]
    > O instalador tentará testar a conectividade para a partilha de ficheiros antes de continuar.  No entanto, se optar por implementar numa rede Virtual existente, o instalador pode não ser capaz de se ligar à partilha de ficheiros e apresenta um aviso, perguntando se deseja continuar.  Verifique as informações de partilha de ficheiros e continuar se eles estão corretos.
    >
    >

   ![Instalador do serviço de aplicações][8]

10. Na página seguinte:
    1. Na **ID da Identity Application** , introduza o GUID para a aplicação estiver a utilizar para a identidade (a partir do Azure AD).
    2. Na **o ficheiro de certificado de aplicação de identidade** caixa, introduza (ou navegar até) a localização do ficheiro de certificado.
    3. Na **palavra-passe de certificado de aplicação de identidade** , introduza a palavra-passe para o certificado. Esta palavra-passe é aquela que anotou quando utilizou o script para criar os certificados.
    4. Na **ficheiro de certificado de raiz do Azure Resource Manager** caixa, introduza (ou navegar até) a localização do ficheiro de certificado.
    5. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações][10]

11. Para cada uma a três caixas de arquivo de certificados, clique em **procurar** e, em seguida, navegue para o ficheiro de certificado adequado. Tem de fornecer a palavra-passe para cada certificado. Estes certificados são aqueles que criou no [passo de certificados necessários criar](azure-stack-app-service-before-you-get-started.md#get-certificates). Clique em **seguinte** após introduzir todas as informações.

    | Box | Exemplo de nome de ficheiro de certificado |
    | --- | --- |
    | **Ficheiro de certificado SSL do serviço de aplicações predefinido** | \_.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado SSL do serviço de aplicações API** | api.appservice.local.AzureStack.external.pfx |
    | **Ficheiro de certificado de SSL de publicador do serviço de aplicações** | ftp.appservice.local.AzureStack.external.pfx |

    Se utilizou um sufixo de domínio diferente ao criar os certificados, os nomes de ficheiro de certificado não utilizem *local. AzureStack.external*. Em vez disso, utilize as informações de domínio personalizado.

    ![Instalador do serviço de aplicações][11]

12. Introduza os detalhes do SQL Server para a instância de servidor utilizada para alojar as bases de dados do fornecedor de recursos do serviço de aplicações e, em seguida, clique em **seguinte**. O instalador valida as propriedades de ligação de SQL. **Tem** introduza o ip interno ou o nome de domínio completamente qualificado para o nome do SQL Server.

    > [!NOTE]
    > O instalador tentará testar a conectividade ao SQl Server antes de continuar.  No entanto, se optar por implementar numa rede Virtual existente, o instalador poderá não conseguir ligar ao SQL Server e apresenta um aviso, perguntando se deseja continuar.  Verifique as informações do SQL Server e continue a se eles estão corretos.
    >
    > Do serviço de aplicações do Azure no Azure 1.3 de pilha e posteriores, o instalador irá verificar que o SQL Server tem de contenção da base de dados ativada ao nível do servidor SQL.  Se não for, será solicitado com a seguinte exceção:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Consulte a [notas de versão do serviço de aplicações do Azure no Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) para obter mais detalhes.
   
    ![Instalador do serviço de aplicações][12]

13. Reveja as opções de SKU e a instância de função. As predefinições são preenchidas com o número mínimo de instâncias e o SKU mínimo para cada função numa implantação ASDK. Para ajudar a planear a implementação, é fornecido um resumo dos requisitos de memória e vCPU. Depois de fazer as seleções, clique em **seguinte**.

     > [!NOTE]
     > Para implementações de produção, siga as orientações no [planeamento de capacidade de funções de servidor do App Service do Azure no Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Função | Instâncias mínimas | SKU mínimo | Notas |
    | --- | --- | --- | --- |
    | Controlador | 1 | Standard_A2 - (2 vCPU, 3584 MB) | Gerencia e mantém o estado de funcionamento da cloud do serviço de aplicações. |
    | Gestão | 1 | Standard_A2 - (2 vCPUs, 3584 MB) | Gere os pontos finais de API e aplicação de serviço do Azure Resource Manager, portais extensões (administração, inquilino, o portal de funções) e o serviço de dados. Para suportar a ativação pós-falha, aumento as instâncias recomendadas para 2. |
    | Publicador | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Publica conteúdo através de implementação de FTP e web. |
    | FrontEnd | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Encaminha os pedidos para aplicativos de serviço de aplicações. |
    | Trabalho partilhado | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Web de anfitriões ou aplicações de API e aplicações de funções do Azure. Pode querer adicionar mais instâncias. Como um operador, pode definir a sua oferta e escolha qualquer escalão SKU. Os escalões tem de ter um mínimo de um vCPU. |

    ![Instalador do serviço de aplicações][14]

    > [!NOTE]
    > **Windows Server 2016 Core não é uma imagem de plataforma suportada para utilização com o serviço de aplicações do Azure no Azure Stack.  Não utilize imagens de avaliação para implementações de produção.  Serviço de aplicações do Azure no Azure Stack requer que o Microsoft.NET 3.5.1 SP1 está ativado na imagem utilizada para a implementação.   Marketplace distribuídos imagens não tem esta funcionalidade ativada do Windows Server 2016, por conseguinte, tem de criar e utilizar uma imagem do Windows Server 2016 com esta opção ativada previamente.**

14. Na **selecione a imagem de plataforma** caixa, escolha a sua imagem de máquina virtual de implantação do Windows Server 2016 daqueles disponíveis no fornecedor de recursos de computação para a cloud de serviço de aplicações. Clique em **Seguinte**.

15. Na página seguinte:
     1. Introduza o nome de utilizador de administrador de máquina virtual de função de trabalho e a palavra-passe.
     2. Introduza o nome de utilizador de administrador de máquina virtual de outras funções e a palavra-passe.
     3. Clique em **Seguinte**.

    ![Instalador do serviço de aplicações][16]

16. Na página de resumo:
    1. Certifique-se seleções que fez. Para fazer alterações, utilize o **Previous** botões para visitar as páginas anteriores.
    2. Se as configurações estão corretas, selecione a caixa de verificação.
    3. Para iniciar a implementação, clique em **seguinte**.

    ![Instalador do serviço de aplicações][17]

17. Na página seguinte:
    1. Controle o progresso da instalação. Serviço de aplicações no Azure Stack demora cerca de 60 minutos para implementar com base nas seleções padrão.
    2. Depois do instalador for concluída com êxito, clique em **saída**.

    ![Instalador do serviço de aplicações][18]

## <a name="post-deployment-steps"></a>Passos de pós-implementação

> [!IMPORTANT]  
> Se forneceu a RP de serviço de aplicações com uma sempre na instância do SQL tem [adicionar as bases de dados appservice_hosting e appservice_metering a um grupo de disponibilidade](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) e sincronizar as bases de dados para evitar qualquer perda de serviço do evento de uma ativação pós-falha de base de dados.

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validar o serviço de aplicações numa instalação do Azure Stack

1. No portal de administração do Azure Stack, aceda a **administração - serviço de aplicações**.

2. Na descrição geral, em estado, verifique que o **Status** apresenta **todas as funções estão prontas**.

    ![Gestão de serviço de aplicações](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> Se optar por implementar numa rede virtual existente e um endereço IP interno para se ligar ao seu servidor de ficheiros, tem de adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB.  Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
> * Origem: Qualquer
> * Intervalo de portas de origem: *
> * Destino: Endereços IP
> * Intervalo de endereços IP de destino: Intervalo de IPs para o servidor de ficheiros
> * Intervalo de portas de destino: 445
> * Protocolo: TCP
> * Ação: Permitir
> * Prioridade: 700
> * Nome: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Experimente o serviço de aplicações no Azure Stack

Depois de implementar e registar o fornecedor de recursos do serviço de aplicações, testá-lo para certificar-se de que os utilizadores podem implementar aplicações web e API.

> [!NOTE]
> Terá de criar uma oferta que tenha o espaço de nomes Microsoft. Web dentro do plano. Em seguida, tem de ter uma subscrição de inquilino que se inscreve para esta oferta. Para obter mais informações, consulte [criar oferta](azure-stack-create-offer.md) e [criar plano](azure-stack-create-plan.md).
>
> *Tem* tem uma subscrição de inquilino para criar aplicativos que usam o serviço de aplicações no Azure Stack. As capacidades de apenas um administrador de serviço pode ser no portal de administração estão relacionados com a administração de fornecedor de recursos do serviço de aplicações. Esses recursos incluem adicionar capacidade, a configuração de origens de implementação e a adição de escalões de Worker e SKUs.
>
> A partir do terceiro technical preview criar a web, API e o Azure funciona aplicações, tem de utilizar o portal de inquilinos e ter uma subscrição de inquilino.

1. No portal de inquilino do Azure Stack, clique em **+ criar um recurso** > **Web + móvel** > **aplicação Web**.

2. Sobre o **aplicação Web** painel, escreva um nome na **aplicação Web** caixa.

3. Sob **grupo de recursos**, clique em **New**. Escreva um nome na **grupo de recursos** caixa.

4. Clique em **plano do Serviço de Aplicações/Localização** > **Criar Novo**.

5. Na **plano do App Service** painel, escreva um nome no **plano do App Service** caixa.

6. Clique em **escalão de preço** > **gratuito partilhado** ou **partilhado partilhado** > **selecione**  >   **OK** > **criar**.

7. Em menos de um minuto, para a nova aplicação web é apresentado um mosaico no dashboard. Clique no mosaico.

8. Sobre o **aplicação Web** painel, clique em **procurar** para ver o Web site predefinido para esta aplicação.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementar um site WordPress, DNN ou Django (opcional)

1. No portal de inquilino do Azure Stack, clique em **+**, vá para o Azure Marketplace, implemente um Web site Django e aguarde pela conclusão com êxito. A plataforma de web Django utiliza um ficheiro com base no sistema base de dados do. Ele não requer quaisquer fornecedores de recursos adicionais, tais como SQL ou MySQL.

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
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png

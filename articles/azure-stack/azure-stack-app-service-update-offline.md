---
title: Atualizar o serviço de aplicações do Azure Offline | Documentos da Microsoft
description: Documentação de orientação detalhada para atualizar o serviço de aplicações do Azure no Azure Stack offline
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
ms.openlocfilehash: 9d941c36499f851f20c41fa6dd01faf14e4192ba
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992775"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Atualização offline do serviço de aplicações do Azure no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!IMPORTANT]
> Aplicar a atualização de 1901 ou mais tarde para o Azure Stack sistema integrado ou implementar o development kit do Azure Stack mais recentes antes de implementar 1.5 de serviço de aplicações do Azure. 

Ao seguir as instruções neste artigo, pode atualizar o [fornecedor de recursos do serviço de aplicações](azure-stack-app-service-overview.md) implementado num ambiente do Azure Stack que é:

* não ligado à Internet
* protegida por serviços de Federação do Active Directory (AD FS).

> [!IMPORTANT]
> Antes de executar a atualização, certifique-se de que já tenha concluído a [implementação do serviço de aplicações do Azure no fornecedor de recursos do Azure Stack](azure-stack-app-service-deploy-offline.md) e que leu o [notas de versão](azure-stack-app-service-release-notes-update-five.md), que acompanham a versão 1.5, para saber mais sobre novas funcionalidades, correções e os problemas conhecidos que podem afetar a sua implementação.

## <a name="run-the-app-service-resource-provider-installer"></a>Execute o instalador de fornecedor de recursos do serviço de aplicações

Para atualizar o fornecedor de recursos do serviço de aplicações num ambiente do Azure Stack, tem de concluir estas tarefas:

1. Transferir o [instalador do serviço de aplicações](https://aka.ms/appsvcupdate4installer)
2. Crie um pacote de atualização offline.
3. Execute o instalador do serviço de aplicações (appservice.exe) e concluir a atualização.

Durante este processo, a atualização irá:

* Detetar a implementação anterior do serviço de aplicações
* Carregar para o armazenamento
* Atualizar todas as funções do serviço de aplicações (controladores, o gerenciamento, front-end, o publicador e o trabalho funções)
* Atualizar definições do conjutno de dimensionamento do Serviço de Aplicações
* Atualizar o manifesto de fornecedor de recursos do serviço de aplicações

## <a name="create-an-offline-upgrade-package"></a>Criar um pacote de atualização offline

Para atualizar o serviço de aplicações num ambiente desligado, primeiro tem de criar um pacote de atualização offline num computador que está ligado à Internet.

1. Executar appservice.exe como administrador

    ![Instalador do serviço de aplicações][1]

2. Clique em **avançadas** > **pacote offline de criar**

    ![Instalador do serviço de aplicações avançada][2]

3. O instalador do serviço de aplicações cria um pacote de atualização offline e apresenta o caminho para o mesmo.  Pode clicar em **Abrir pasta** para abrir a pasta no Explorador de ficheiros.

4. Copie o instalador (AppService.exe) e o pacote de atualização offline à sua máquina de anfitrião do Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Conclua a atualização do serviço de aplicações no Azure Stack

> [!IMPORTANT]
> O instalador do serviço de aplicações tem de ser executado numa máquina que pode alcançar o Azure Stack administrador do Azure Resource Manager ponto final.
>
>

1. Execute appservice.exe como administrador.

    ![Instalador do serviço de aplicações][1]

2. Clique em **avançadas** > **concluir instalação offline ou atualizar**.

    ![Instalador do serviço de aplicações avançada][2]

3. Navegue até à localização do pacote de atualização offline que criou anteriormente e, em seguida, clique em **seguinte**.

4. Reveja e aceite os termos de licença de Software do Microsoft e, em seguida, clique em **seguinte**.

5. Reveja e aceite os termos de licença de terceiros e, em seguida, clique em **seguinte**.

6. Certifique-se de que o ponto final do Azure Stack Azure Resource Manager e o inquilino do Active Directory informações estão corretas. Se usou as configurações padrão durante a implementação do Development Kit do Azure Stack, pode aceitar os valores predefinidos aqui. No entanto, se as opções personalizado quando implementou o Azure Stack, tem de editar os valores nessa janela. Por exemplo, se usar o sufixo de domínio *mycloud.com*, tem de alterar para o ponto de final do Azure Stack Azure Resource Manager *management.region.mycloud.com*. Depois de confirmar suas informações, clique em **seguinte**.

    ![Informações de nuvem do Azure Stack][3]

7. Na página seguinte:

   1. Clique nas **Connect** junto aos **subscrições do Azure Stack** caixa.
        * Se estiver a utilizar o Azure Active Directory (Azure AD), introduza a conta de administrador do Azure AD e a palavra-passe que forneceu quando implementou o Azure Stack. Clique em **iniciar sessão**.
        * Se estiver a utilizar os serviços de Federação do Active Directory (AD FS), forneça a sua conta de administrador. Por exemplo, _cloudadmin@azurestack.local_. Introduza a palavra-passe e clique em **sessão**.
   2. Na **subscrições do Azure Stack** caixa, selecione a **subscrição do fornecedor predefinido**.
   3. Na **localizações do Azure Stack** caixa, selecione a localização que corresponde à região que está a implementar. Por exemplo, seleccione **local** se sua a implementar o Development Kit do Azure Stack.
   4. Se for detetada uma implementação existente do serviço de aplicações, em seguida, a conta de armazenamento e de grupo de recursos será preenchida e a cinzento.
   5. Clique em **seguinte** para rever o resumo da atualização.

    ![Instalação do serviço de aplicações detetada][4]

8. Na página de resumo:
   1. Certifique-se seleções que fez. Para fazer alterações, utilize o **Previous** botões para visitar as páginas anteriores.
   2. Se as configurações estão corretas, selecione a caixa de verificação.
   3. Para iniciar a atualização, clique em **seguinte**.

       ![Resumo de atualização do serviço de aplicações][5]

9. Página de progresso da atualização:
    1. Controle o progresso da atualização. A duração da atualização do serviço de aplicações no Azure Stack varia depende do número de instâncias de função implementadas.
    2. Após a atualização for concluída com êxito, clique em **saída**.

        ![Progresso da atualização de serviço de aplicações][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Passos Seguintes

Também pode tentar outros [plataforma como um serviço de serviço (PaaS)](azure-stack-tools-paas-services.md).

* [Fornecedor de recursos do SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Fornecedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md)

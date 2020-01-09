---
title: Como criar e implantar um serviço de nuvem | Microsoft Docs
description: Saiba como criar e implantar um serviço de nuvem usando o portal do Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 53f53976b20359afc45abe1b25ca60325b5d6a2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75386175"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implantar um serviço de nuvem
O portal do Azure fornece duas maneiras de criar e implantar um serviço de nuvem: *criação rápida* e *criação personalizada*.

Este artigo explica como usar o método criação rápida para criar um novo serviço de nuvem e, em seguida, usar **carregar** para carregar e implantar um pacote de serviço de nuvem no Azure. Quando você usa esse método, o portal do Azure disponibiliza links convenientes para concluir todos os requisitos conforme o uso. Se você estiver pronto para implantar seu serviço de nuvem ao criá-lo, poderá fazer ambos ao mesmo tempo usando a criação personalizada.

> [!NOTE]
> Se você planeja publicar seu serviço de nuvem do Azure DevOps, use a criação rápida e, em seguida, configure a publicação do Azure DevOps no início rápido do Azure ou no painel. Para obter mais informações, consulte [entrega contínua para o Azure usando o Azure DevOps][TFSTutorialForCloudService]ou consulte a ajuda para a página de **início rápido** .
>
>

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implantar um aplicativo como um serviço de nuvem no Azure:

* **Definição de serviço**  
  O arquivo de definição de serviço de nuvem (. csdef) define o modelo de serviço, incluindo o número de funções.
* **Configuração do serviço**  
  O arquivo de configuração do serviço de nuvem (. cscfg) fornece definições de configuração para o serviço de nuvem e funções individuais, incluindo o número de instâncias de função.
* **Pacote de serviço**  
  O pacote de serviço (. cspkg) contém o código do aplicativo e as configurações e o arquivo de definição de serviço.

Você pode aprender mais sobre eles e como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparar seu aplicativo
Antes de implantar um serviço de nuvem, você deve criar o pacote de serviço de nuvem (. cspkg) do código do aplicativo e um arquivo de configuração de serviço de nuvem (. cscfg). O SDK do Azure fornece ferramentas para preparar esses arquivos de implantação necessários. Você pode instalar o SDK na página de [downloads do Azure](https://azure.microsoft.com/downloads/) , no idioma em que você prefere desenvolver o código do aplicativo.

Três recursos de serviço de nuvem exigem configurações especiais antes de exportar um pacote de serviço:

* Se você quiser implantar um serviço de nuvem que usa protocolo SSL (SSL) para criptografia de dados, [configure seu aplicativo](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.
* Se você quiser configurar conexões de Área de Trabalho Remota para instâncias de função, [Configure as funções](cloud-services-role-enable-remote-desktop-new-portal.md) para área de trabalho remota.
* Se você quiser configurar o monitoramento detalhado para seu serviço de nuvem, habilite Diagnóstico do Azure para o serviço de nuvem. O *monitoramento mínimo* (o nível de monitoramento padrão) usa contadores de desempenho coletados dos sistemas operacionais do host para instâncias de função (máquinas virtuais). O *monitoramento detalhado* reúne métricas adicionais com base nos dados de desempenho nas instâncias de função para permitir uma análise mais próxima dos problemas que ocorrem durante o processamento do aplicativo. Para saber como habilitar Diagnóstico do Azure, consulte [habilitando o diagnóstico no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço de nuvem com implantações de funções Web ou funções de trabalho, você deve [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se você ainda não instalou o SDK do Azure, clique em **instalar o SDK do Azure** para abrir a [página de downloads do Azure](https://azure.microsoft.com/downloads/)e, em seguida, baixe o SDK para o idioma no qual você prefere desenvolver seu código. (Você terá a oportunidade de fazer isso mais tarde).
* Se qualquer instância de função exigir um certificado, crie os certificados. Os serviços de nuvem exigem um arquivo. pfx com uma chave privada. Você pode carregar os certificados no Azure enquanto cria e implanta o serviço de nuvem.

## <a name="create-and-deploy"></a>Criar e implementar
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **criar um recurso > computação**e, em seguida, role para baixo até e clique em **serviço de nuvem**.

    ![Publicar seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. No painel novo **serviço de nuvem** , insira um valor para o **nome DNS**.
4. Crie um novo **grupo de recursos** ou selecione um existente.
5. Selecione uma **Localização**.
6. Clique em **pacote**. Isso abre o painel **carregar um pacote** . Preencha os campos obrigatórios. Se qualquer uma de suas funções contiver uma única instância, certifique-se de **implantar mesmo se uma ou mais funções contiverem uma única instância** estiver selecionada.
7. Verifique se **Iniciar implantação** está selecionado.
8. Clique em **OK** , que fechará o painel **carregar um pacote** .
9. Se você não tiver nenhum certificado para adicionar, clique em **criar**.

    ![Publicar seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Carregar um certificado
Se o pacote de implantação foi [configurado para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), você pode carregar o certificado agora.

1. Selecione **certificados**e, no painel **adicionar certificados** , selecione o arquivo. pfx do certificado SSL e forneça a **senha** para o certificado,
2. Clique em **anexar certificado**e, em seguida, clique em **OK** no painel **adicionar certificados** .
3. Clique em **criar** no painel **serviço de nuvem** . Quando a implantação tiver atingido o status **pronto** , você poderá prosseguir para as próximas etapas.

    ![Publicar seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifique se a implantação foi concluída com êxito
1. Clique na instância do serviço de nuvem.

    O status deve mostrar que o serviço está **em execução**.
2. Em **Essentials**, clique na **URL do site** para abrir o serviço de nuvem em um navegador da Web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerencie seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).




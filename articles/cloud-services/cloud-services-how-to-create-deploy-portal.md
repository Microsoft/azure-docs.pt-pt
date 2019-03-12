---
title: Como criar e implementar um serviço em nuvem | Documentos da Microsoft
description: Saiba como criar e implementar um serviço cloud no portal do Azure.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: a6cf2276da463f71f008c4bfb6eee4c232b18308
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551709"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço cloud
O portal do Azure fornece duas formas para criar e implementar um serviço cloud: *Criação rápida* e *criação personalizada*.

Este artigo explica como utilizar o método de criação rápida para criar um novo serviço cloud e, em seguida, utilizar **carregar** para carregar e implementar um pacote de serviço cloud no Azure. Quando utiliza este método, o portal do Azure faz ligações práticas disponíveis para concluir todos os requisitos de medida que avança. Se estiver pronto para implementar o seu serviço cloud quando a criar, pode fazer os dois ao mesmo tempo, usando criação personalizada.

> [!NOTE]
> Se planeia publicar o seu serviço cloud do Azure DevOps, utilize a criação rápida e, em seguida, configurar a publicação de DevOps do Azure de início rápido do Azure ou o dashboard. Para obter mais informações, consulte [entrega contínua para o Azure através do Azure DevOps][TFSTutorialForCloudService], ou consulte a ajuda para o **início rápido** página.
>
>

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implementar uma aplicação como um serviço cloud no Azure:

* **Definição de serviço**  
  O ficheiro de definição de serviço cloud (. csdef) define o modelo de serviço, incluindo o número de funções.
* **Configuração do serviço**  
  O ficheiro de configuração do serviço cloud (. cscfg) fornece funções de serviços e individuais, incluindo o número de instâncias de função de definições de configuração para a cloud.
* **Pacote de serviço**  
  O pacote de serviço (. cspkg) contém o código do aplicativo e as configurações e o ficheiro de definição de serviço.

Pode saber mais sobre estas e como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparar a sua aplicação
Antes de poder implementar um serviço em nuvem, tem de criar o pacote de serviço cloud (. cspkg) de código da aplicação e um ficheiro de configuração do serviço cloud (. cscfg). O SDK do Azure fornece ferramentas para preparar estes ficheiros de implementação necessária. Pode instalar o SDK do [transferências do Azure](https://azure.microsoft.com/downloads/) página, no idioma que preferir desenvolver o código de aplicação.

Três recursos de serviço de nuvem requerem configurações especiais antes de exportar um pacote de serviço:

* Se pretender implementar um serviço cloud que utiliza o Secure Sockets Layer (SSL) para a encriptação de dados, [configurar a sua aplicação](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.
* Se pretender configurar ligações de ambiente de trabalho remoto para instâncias de função [configuram as funções](cloud-services-role-enable-remote-desktop-new-portal.md) para ambiente de trabalho remoto.
* Se pretender configurar verboso de monitorização do serviço em nuvem, ative o diagnóstico do Azure para o serviço cloud. *Monitorização mínima* (a predefinição do nível de monitoramento) utiliza contadores de desempenho recolhidas a partir dos sistemas de operativos de anfitrião para instâncias de função (máquinas virtuais). *Monitorização verboso* recolhe métricas adicionais com base nos dados de desempenho dentro as instâncias de função para ativar a análise mais cuidada relativamente dos problemas que ocorrem durante o processamento do aplicativo. Para saber como ativar o diagnóstico do Azure, consulte [ativar os diagnósticos no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço em nuvem com implementações de funções da web ou funções de trabalho, deve [criar o pacote de serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se ainda não instalou o SDK do Azure, clique em **instalar SDK do Azure** para abrir o [página de transferências do Azure](https://azure.microsoft.com/downloads/)e, em seguida, transferir o SDK para o idioma que preferir desenvolver o código. (Terá uma oportunidade de fazê-lo mais tarde.)
* Se quaisquer instâncias de função requerem um certificado, crie os certificados. Serviços cloud necessitam de um ficheiro. pfx com uma chave privada. Pode carregar os certificados para o Azure, como criar e implementar o serviço em nuvem.

## <a name="create-and-deploy"></a>Criar e implementar
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **criar um recurso > computação**e, em seguida, role para baixo e clique em **serviço Cloud**.

    ![Publicar o seu serviço cloud](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. No novo **serviço em nuvem** painel, introduza um valor para o **nome DNS**.
4. Criar uma nova **grupo de recursos** ou selecione um existente.
5. Selecione uma **Localização**.
6. Clique em **pacote**. Esta ação abre o **carregar um pacote** painel. Preencha os campos obrigatórios. Se qualquer uma das suas funções contenham uma única instância, certifique-se **implementar mesmo que uma ou mais funções contenham uma única instância** está selecionada.
7. Certifique-se de que **iniciar implementação** está selecionada.
8. Clique em **OK** que irá fechar a **carregar um pacote** painel.
9. Se não tiver quaisquer certificados para adicionar, clique em **criar**.

    ![Publicar o seu serviço cloud](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Carregar um certificado
Se o seu pacote de implementação foi [configurado para utilizar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), pode carregar o certificado agora.

1. Selecione **certificados**e, no **adicionar certificados** painel, selecione o ficheiro. pfx do certificado SSL e, em seguida, forneça o **palavra-passe** para o certificado,
2. Clique em **certificado Attach**e, em seguida, clique em **OK** sobre o **adicionar certificados** painel.
3. Clique em **Create** sobre o **serviço Cloud** painel. Quando a implantação tiver atingido o **pronto** Estado, pode avançar para os passos seguintes.

    ![Publicar o seu serviço cloud](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Certifique-se a implementação foi concluída com êxito
1. Clique na instância de serviço de nuvem.

    O estado deve mostrar que o serviço está **em execução**.
2. Sob **Essentials**, clique nas **URL do Site** para abrir o seu serviço cloud num navegador da web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Passos Seguintes
* [Configuração geral do seu serviço cloud](cloud-services-how-to-configure-portal.md).
* Configurar uma [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o seu serviço cloud](cloud-services-how-to-manage-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).

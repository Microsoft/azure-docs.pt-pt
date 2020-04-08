---
title: Como criar e implementar um serviço de nuvem [ Microsoft Docs
description: Aprenda a criar e implementar um serviço de nuvem utilizando o portal Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 63cf864a3f3b92728ad613ac45542bdbce2c9858
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811332"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço de nuvem
O portal Azure fornece duas formas de criar e implementar um serviço na nuvem: *Quick Create* and *Custom Create*.

Este artigo explica como usar o método Quick Create para criar um novo serviço na nuvem e, em seguida, usar **o Upload** para carregar e implantar um pacote de serviço na nuvem em Azure. Quando utiliza este método, o portal Azure disponibiliza links convenientes para completar todos os requisitos à medida que avança. Se estiver pronto para implementar o seu serviço de cloud quando o criar, pode fazer ambos ao mesmo tempo usando o Custom Create.

> [!NOTE]
> Se planeia publicar o seu serviço na nuvem a partir de Azure DevOps, utilize o Quick Create e, em seguida, instale a publicação azure DevOps a partir do Azure Quickstart ou do dashboard. Para mais informações, consulte a [Entrega Contínua ao Azure utilizando o Azure DevOps,][TFSTutorialForCloudService]ou consulte a ajuda para a página **Dearranque Rápido.**
>
>

## <a name="concepts"></a>Conceitos
São necessários três componentes para implementar uma aplicação como serviço na nuvem em Azure:

* **Definição de Serviço**  
  O ficheiro de definição de serviço na nuvem (.csdef) define o modelo de serviço, incluindo o número de funções.
* **Configuração do serviço**  
  O ficheiro de configuração do serviço em nuvem (.cscfg) fornece configurações de configuração para o serviço de nuvem e funções individuais, incluindo o número de instâncias de funções.
* **Pacote de serviço**  
  O pacote de serviço (.cspkg) contém o código de aplicação e configurações e o ficheiro de definição de serviço.

Você pode aprender mais sobre estes e como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Prepare a sua aplicação
Antes de poder implementar um serviço na nuvem, deve criar o pacote de serviço na nuvem (.cspkg) a partir do seu código de aplicação e de um ficheiro de configuração de serviço na nuvem (.cscfg). O Azure SDK fornece ferramentas para a preparação destes ficheiros de implementação necessários. Pode instalar o SDK a partir da página [Descarregamentos Do Azure,](https://azure.microsoft.com/downloads/) no idioma em que prefere desenvolver o seu código de aplicação.

Três funcionalidades de serviço na nuvem requerem configurações especiais antes de exportar um pacote de serviço:

* Se pretender implementar um serviço na nuvem que utilize segurança em camadas de transporte (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), para encriptação de dados, [configure a sua aplicação](cloud-services-configure-ssl-certificate-portal.md#modify) para TLS.
* Se pretender configurar as ligações de ambiente de trabalho remoto para as instâncias de funções, [configure as funções](cloud-services-role-enable-remote-desktop-new-portal.md) para Desktop Remoto.
* Se pretender configurar a monitorização verbosa para o seu serviço na nuvem, ative o Azure Diagnostics para o serviço na nuvem. *A monitorização mínima* (o nível de monitorização predefinido) utiliza contadores de desempenho recolhidos dos sistemas operativos hospedeiros para as instâncias de funções (máquinas virtuais). *A monitorização verbose* reúne métricas adicionais com base em dados de desempenho dentro das instâncias de função para permitir uma análise mais aprofundada dos problemas que ocorrem durante o processamento da aplicação. Para saber como ativar o Azure Diagnostics, consulte [o Enableing diagnostics in Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço na nuvem com implementações de funções web ou funções de trabalhador, deve [criar o pacote de serviços](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se ainda não instalou o Azure SDK, clique em Instalar o **Azure SDK** para abrir a [página Descarregamentos do Azure](https://azure.microsoft.com/downloads/)e, em seguida, descarregue o SDK para o idioma em que prefere desenvolver o seu código. (Terá a oportunidade de fazer isto mais tarde.)
* Se algum caso de função necessitar de um certificado, crie os certificados. Os serviços em nuvem requerem um ficheiro .pfx com uma chave privada. Pode enviar os certificados para o Azure à medida que cria e implanta o serviço de cloud.

## <a name="create-and-deploy"></a>Criar e implementar
1. Faça login no [portal Azure.](https://portal.azure.com/)
2. Clique **em Criar um recurso > Compute**, e depois desloque-se para baixo e clique no Cloud **Service**.

    ![Publique o seu serviço na nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. No novo painel **cloud service,** insira um valor para o **nome DNS**.
4. Crie um novo **Grupo de Recursos** ou selecione um existente.
5. Selecione um **Local**.
6. Clique **em Pacote**. Isto abre o upload de um painel de **pacote.** Preencha os campos necessários. Se alguma das suas funções contiver uma única instância, **certifique-se de que o Deploy mesmo que uma ou mais funções contenham uma única instância.**
7. Certifique-se de que a **implementação do Início** é selecionada.
8. Clique **em OK** que fechará o painel de **pacotes do Upload.**
9. Se não tiver certificados para adicionar, clique em **Criar**.

    ![Publique o seu serviço na nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Faça upload de um certificado
Se o seu pacote de implementação foi [configurado para utilizar certificados,](cloud-services-configure-ssl-certificate-portal.md#modify)pode fazer o upload do certificado agora.

1. Selecione **Certificados,** e no painel **de certificados Adicionar,** selecione o certificado TLS/SSL .pfx e, em seguida, forneça a **Palavra-passe** para o certificado,
2. Clique no **certificado Anexar**e, em seguida, clique **OK** no painel **de certificados Adicionar.**
3. Clique em **Criar** no painel **do Serviço cloud.** Quando a implantação atingir o estado **de Ready,** pode passar aos próximos passos.

    ![Publique o seu serviço na nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifique a sua implementação concluída com sucesso
1. Clique na instância de serviço na nuvem.

    O estado deve mostrar que o serviço está **em execução**.
2. Em **Essentials,** clique no URL do **Site** para abrir o seu serviço de nuvem num navegador web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço](cloud-services-how-to-configure-portal.md)de nuvem.
* Configure um nome de [domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerencie o seu serviço de cloud](cloud-services-how-to-manage-portal.md).
* Configure [os certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).




---
title: Como criar e implementar um serviço de cloud Microsoft Docs
description: Aprenda a usar o método Quick Create para criar um serviço de cloud e use o Upload para carregar e implementar um pacote de serviço na nuvem em Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: fca14ed4e3b338f9b49f949af7498dfd49990d3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142388"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço de nuvem
O portal Azure fornece duas formas de criar e implementar um serviço em nuvem: *Quick Create* e *Custom Create*.

Este artigo explica como usar o método Quick Create para criar um novo serviço na nuvem e, em seguida, usar **upload** para carregar e implementar um pacote de serviço de nuvem em Azure. Ao utilizar este método, o portal Azure disponibiliza links convenientes para completar todos os requisitos à medida que avança. Se estiver pronto para implementar o seu serviço de nuvem quando o criar, pode fazer ambos ao mesmo tempo usando o Custom Create.

> [!NOTE]
> Se planeia publicar o seu serviço em nuvem a partir da Azure DevOps, use a Quick Create e, em seguida, instale a publicação Azure DevOps a partir do Azure Quickstart ou do dashboard. Para obter mais informações, consulte [a Entrega Contínua ao Azure utilizando Azure DevOps,][TFSTutorialForCloudService]ou consulte a ajuda para a página **'Início Rápido'.**
>
>

## <a name="concepts"></a>Conceitos
São necessários três componentes para implementar uma aplicação como serviço em nuvem em Azure:

* **Definição de serviço**  
  O ficheiro de definição de serviço em nuvem (.csdef) define o modelo de serviço, incluindo o número de funções.
* **Configuração de serviço**  
  O ficheiro de configuração do serviço de nuvem (.cscfg) fornece configurações de configuração para o serviço na nuvem e funções individuais, incluindo o número de instâncias de função.
* **Pacote de Serviço**  
  O pacote de serviço (.cspkg) contém o código de aplicação e configurações e o ficheiro de definição de serviço.

Você pode saber mais sobre estes e como criar um pacote [aqui.](cloud-services-model-and-package.md)

## <a name="prepare-your-app"></a>Prepare a sua aplicação
Antes de poder implementar um serviço de cloud, tem de criar o pacote de serviços de nuvem (.cspkg) a partir do seu código de aplicação e de um ficheiro de configuração de serviço na nuvem (.cscfg). O Azure SDK fornece ferramentas para preparar estes ficheiros de implantação necessários. Pode instalar o SDK a partir da página [Azure Downloads,](https://azure.microsoft.com/downloads/) no idioma em que prefere desenvolver o seu código de aplicação.

Três funcionalidades de serviço em nuvem requerem configurações especiais antes de exportar um pacote de serviços:

* Se pretender implementar um serviço de cloud que utilize a Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL), para encriptação de dados, [configure a sua aplicação](cloud-services-configure-ssl-certificate-portal.md#modify) para TLS.
* Se pretender configurar as ligações de Ambiente de Trabalho Remoto para instâncias de função, [configuure as funções](cloud-services-role-enable-remote-desktop-new-portal.md) para Desktop Remoto.
* Se quiser configurar a monitorização verbose para o seu serviço na nuvem, ative o Azure Diagnostics para o serviço na nuvem. *A monitorização mínima* (o nível de monitorização predefinido) utiliza contadores de desempenho recolhidos dos sistemas operativos hospedeiros para instâncias de função (máquinas virtuais). *A monitorização verbose* recolhe métricas adicionais com base em dados de desempenho dentro das instâncias de função para permitir uma análise mais aprofundada dos problemas que ocorrem durante o processamento da aplicação. Para saber como ativar o Azure Diagnostics, consulte [ativar os diagnósticos em Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço em nuvem com implementações de funções web ou funções de trabalhador, tem de [criar o pacote de serviços](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar
* Se ainda não instalou o Azure SDK, clique em **Instalar Azure SDK** para abrir a [página Azure Downloads](https://azure.microsoft.com/downloads/)e, em seguida, descarregue o SDK para o idioma em que prefere desenvolver o seu código. (Terá a oportunidade de fazer isto mais tarde.)
* Se alguma instância de papel necessitar de um certificado, crie os certificados. Os serviços em nuvem requerem um ficheiro .pfx com uma chave privada. Pode fazer o upload dos certificados para Azure à medida que criar e implementar o serviço de cloud.

## <a name="create-and-deploy"></a>Criar e implementar
1. Faça login no [portal Azure](https://portal.azure.com/).
2. Clique **em Criar um recurso > Compute**e, em seguida, desloque-se para baixo e clique no Cloud **Service**.

    ![Publique o seu serviço na nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. No novo painel **cloud service,** insira um valor para o **nome DNS**.
4. Crie um novo **Grupo de Recursos** ou selecione um existente.
5. Selecione uma **localização**.
6. Clique **em Pacote**. Isto abre o **painel de pacotes upload.** Preencha os campos necessários. Se alguma das suas funções contiver uma única instância, **certifique-se de implementar mesmo que uma ou mais funções contenham uma única instância.**
7. Certifique-se de que **a implementação do Start** está selecionada.
8. Clique **em OK** que fechará o painel de embalagem do **Upload.**
9. Se não tiver quaisquer certificados a adicionar, clique em **Criar**.

    ![Publique o seu serviço na nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Faça upload de um certificado
Se o seu pacote de implantação foi [configurado para utilizar certificados,](cloud-services-configure-ssl-certificate-portal.md#modify)pode fazer o upload do certificado agora.

1. Selecione **Certificados**, e no painel de **certificados Adicionar,** selecione o certificado TLS/SSL .pfx e, em seguida, forneça a **Palavra-passe** para o certificado,
2. Clique **em Anexar o certificado**e, em seguida, clique em **OK** no painel **de certificados Adicionar.**
3. Clique em **Criar** no painel de serviço de **nuvem.** Quando a colocação tiver atingido o estado **de Pronto,** pode proceder aos próximos passos.

    ![Publique o seu serviço na nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifique se a sua implementação foi concluída com sucesso
1. Clique na instância de serviço na nuvem.

    O estado deve mostrar que o serviço está **em funcionamento.**
2. Em **Essencial,** clique no URL do **Site** para abrir o seu serviço de nuvem num navegador web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Passos seguintes
* [Configuração geral do seu serviço na nuvem](cloud-services-how-to-configure-portal.md).
* Configure um [nome de domínio personalizado.](cloud-services-custom-domain-name-portal.md)
* [Gerencie o seu serviço na nuvem](cloud-services-how-to-manage-portal.md).
* Configure [os certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md).




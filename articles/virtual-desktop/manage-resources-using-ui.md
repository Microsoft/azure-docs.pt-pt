---
title: Implementar a ferramenta de gestão - Azure
description: Como instalar uma ferramenta de interface de utilizador para gerir os recursos de pré-visualização da área de Trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: c4332f4aac40fb15c5612dfbc673727935e66717
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057590"
---
# <a name="tutorial-deploy-a-management-tool"></a>Tutorial: Implementar uma ferramenta de gerenciamento

A ferramenta de gestão fornece uma interface de utilizador (IU) para gerir recursos do Microsoft Virtual Desktop Preview. Neste tutorial, irá aprender como implantar e ligue-se à ferramenta de gestão.

>[!NOTE]
>Estas instruções destinam-se uma configuração específica de pré-visualização de área de Trabalho Virtual do Windows que pode ser utilizada com os processos existentes da sua organização.

## <a name="important-considerations"></a>Considerações importantes

Uma vez que a aplicação necessita de consentimento para interagir com a área de Trabalho Virtual do Windows, esta ferramenta não suporta cenários de empresa-empresa (B2B). Subscrição do cada inquilino Azure Active Directory (AAD) terá sua própria implantação à parte da ferramenta de gestão.

Essa ferramenta de gestão é um exemplo. Microsoft irá fornecer atualizações de qualidade e de segurança importante. O [código-fonte está disponível no GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Os clientes e parceiros são encorajados a personalizar a ferramenta de acordo com as suas necessidades de negócios.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que precisa para executar o modelo Azure Resource Manager

Antes de implementar o modelo Azure Resource Manager, terá de um utilizador do Azure Active Directory da conta que:

- Desativou Azure multi-factor Authentication (MFA)
- Tem permissão para criar recursos na sua subscrição do Azure
- Tem permissões para ler o seu inquilino de área de Trabalho Virtual do Windows

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Executar o modelo Azure Resource Manager para aprovisionar o gerenciamento da interface do Usuário

Antes de começar, certifique-se as aplicações de servidor e cliente têm consentimento, visitando a [Windows Virtual Desktop consentimento página](https://rdweb.wvd.microsoft.com) para o Azure Active Directory (AAD) representado.

Siga estas instruções para implementar o modelo de gestão de recursos do Azure:

1. Vá para o [página GitHub do Azure RDS-modelos](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implemente o modelo para o Azure.
    - Se estiver a implementar numa subscrição Enterprise, desloque para baixo e selecione **implementar no Azure**. Ver [documentação de orientação para os parâmetros de modelo](#guidance-for-template-parameters).
    - Se estiver a implementar uma subscrição do fornecedor de soluções Cloud, siga estas instruções para implementar no Azure:
        1. Desloque para baixo e clique com botão direito **implementar no Azure**, em seguida, selecione **localização da ligação de cópia**.
        2. Abra um editor de texto como o bloco de notas e cole a ligação existe.
        3. Logo após <https://portal.azure.com/> e antes da hashtag (#), introduza um arroba (@) seguido do nome de domínio do inquilino. Eis um exemplo do formato: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Inicie sessão no portal do Azure como um utilizador com permissões de administrador/contribuinte à subscrição do fornecedor de soluções Cloud.
        5. Cole a ligação que copiou para o editor de texto na barra de endereço.

### <a name="guidance-for-template-parameters"></a>Documentação de orientação para os parâmetros de modelo
Eis como introduzir parâmetros para configurar a ferramenta:

- Este é o URL de Mediador de área de trabalho remota:  <https://rdbroker.wvd.microsoft.com/>
- Este é o URL de recurso:  <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- Utilize as suas credenciais do AAD com a MFA desativada para iniciar sessão no Azure. Ver [o que precisa para executar o modelo Azure Resource Manager](#what-you-need-to-run-the-azure-resource-manager-template).
- Utilize um nome exclusivo para a aplicação que será registado no Azure Active Directory para a ferramenta de gestão; Por exemplo, Apr3UX.

## <a name="use-the-management-tool"></a>Utilize a ferramenta de gestão

Depois do GitHub do Azure Resource Manager modelo for concluída, encontrará um grupo de recursos que contém dois serviços de aplicação, juntamente com um plano do serviço de aplicações no portal do Azure.

Siga estas instruções para iniciar a ferramenta:

1. Selecione o recurso de serviços aplicacionais do Azure com o nome que indicou no modelo (por exemplo, Apr3UX) e navegue para o URL associado-lo. Por exemplo, <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Inicie sessão com as credenciais da sua área de Trabalho Virtual do Windows.
3. Quando lhe for pedido para escolher um grupo de inquilino, selecione **grupo de inquilino predefinido** na lista pendente.

> [!NOTE]
> Se tiver um grupo personalizado de inquilino, introduza o nome manualmente em vez de selecionar a lista pendente.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como implementar e ligar-se à ferramenta de gestão, pode aprender a utilizar o Azure Service Health para monitorizar problemas de serviço e aconselhamentos sobre o estado de funcionamento.

> [!div class="nextstepaction"]
> [Configurar o tutorial de alertas do serviço](./set-up-service-alerts.md)

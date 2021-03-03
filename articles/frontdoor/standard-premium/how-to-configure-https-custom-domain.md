---
title: Configurar HTTPS para o seu domínio personalizado numa configuração SKU Standard/Premium Door Standard/Premium
description: Neste artigo, você aprenderá a bordo de um domínio personalizado para Azure Front Door Standard/Premium SKU.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: c2edf11939996156c2b589b0b7876ae1b01466e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740822"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Configurar HTTPS num domínio personalizado Standard/Premium SKU (Preview) da porta frontal utilizando o portal Azure

> [!NOTE]
> Esta documentação destina-se ao Azure Front Door Standard/Premium (Preview). À procura de informações sobre a Porta da Frente Azure? Ver [aqui](../front-door-overview.md).

O Azure Front Door Standard/Premium permite a entrega segura de TLS às suas aplicações por padrão quando um domínio personalizado é adicionado. Ao utilizar o protocolo HTTPS no seu domínio personalizado, garante que os seus dados sensíveis são entregues de forma segura com encriptação TLS/SSL quando são enviados através da internet. Quando o browser está ligado a um site por HTTPS, valida o certificado de segurança do site e verifica que este é emitido por uma autoridade de certificação legítima. Este processo oferece segurança e protege as suas aplicações Web de ataques.

A Azure Front Door Standard/Premium suporta certificado gerido pela Azure e certificados geridos pelo cliente. A Azure Front Door por predefinição permite automaticamente HTTPS a todos os seus domínios personalizados utilizando certificados geridos Azure. Não são necessários passos adicionais para obter um certificado gerido aZure. Um certificado é criado durante o processo de validação de domínio. Também pode utilizar o seu próprio certificado integrando o Azure Front Door Standard/Premium com o seu Cofre chave.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Preview) está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [**termos de utilização suplementares para pré-visualizações do Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

* Antes de configurar HTTPS para o seu domínio personalizado, tem primeiro de criar um perfil Standard/Premium da Porta Frontal Azure. Para obter mais informações, consulte [Quickstart: Crie um perfil Standard/Premium da Porta Frontal Azure](create-front-door-portal.md).

* Se ainda não tem um domínio personalizado, tem primeiro de comprar um com um fornecedor de domínio. Por exemplo, veja [Buy a custom domain name](../../app-service/manage-custom-dns-buy-domain.md) (Comprar um nome de domínio personalizado).

* Se estiver a utilizar o Azure para hospedar os seus [domínios DNS,](../../dns/dns-overview.md)tem de delegar o sistema de nome de domínio do fornecedor de domínio (DNS) num DNS Azure. Para obter mais informações, consulte [delegado de um domínio para Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Caso contrário, se estiver a utilizar um fornecedor de domínio para manusear o seu domínio DNS, tem de validar manualmente o domínio introduzindo registos DNS TXT solicitados.

## <a name="azure-managed-certificates"></a>Certificados geridos Azure

1. Em Definições para o seu perfil Azure Front Door Standard/Premium, selecione **Domínios** e, em seguida, selecione **+ Adicione** para adicionar um novo domínio.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Screenshot da página de aterragem de configuração de domínio.":::

1. Na página **de domínio Adicionar,** para *gestão de DNS* selecione a opção **DNS gerida pelo Azure.** 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Screen shot de adicionar uma página de domínio com DNS gerido Azure selecionado.":::

1. Valide e associe o domínio personalizado a um ponto final, seguindo os passos para permitir o [domínio personalizado.](how-to-add-custom-domain.md)

1. Uma vez que o domínio personalizado é associado ao ponto final com sucesso, um certificado gerido Azure é implantado na Porta da Frente. Este processo pode demorar alguns minutos a ser concluído.

## <a name="using-your-own-certificate"></a>Usando o seu próprio certificado

Também pode optar por utilizar o seu próprio certificado TLS. Este certificado deve ser importado para um Cofre de Chaves Azure antes de poder usá-lo com o Azure Front Door Standard/Premium. Consulte [a importação de um certificado](../../key-vault/certificates/tutorial-import-certificate.md) para Azure Key Vault. 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Prepare a conta e o certificado do Azure Key Vault
 
1. Tem de ter uma conta Azure Key Vault em funcionamento na mesma subscrição que o Seu Azure Front Door Standard/Premium que pretende ativar HTTPS personalizados. Se não tiver uma, crie uma conta do Azure Key Vault.

    > [!WARNING]
    > A Azure Front Door suporta atualmente apenas contas key vault na mesma subscrição que a configuração da porta frontal. Escolher um Cofre-Chave sob uma subscrição diferente do seu Azure Front Door Standard/Premium resultará numa falha.

1. Se já tiver um certificado, pode enviá-lo diretamente para a sua conta Azure Key Vault. Caso contrário, crie um novo certificado diretamente através do Azure Key Vault a partir de um dos parceiros Certificate Authorities com o qual a Azure Key Vault se integra. Faça o upload do seu certificado como objeto de **certificado,** em vez de **um segredo.**

    > [!NOTE]
    > Para o seu próprio certificado TLS/SSL, a Porta Frontal não suporta certificados com algoritmos de criptografia EC.

#### <a name="register-azure-front-door"></a>Registar porta frontal Azure

Registe o principal de serviço da Azure Front Door como uma aplicação no seu Azure Ative Directory via PowerShell.

> [!NOTE]
> Esta ação requer permissões de Administrador Global, e precisa ser executada apenas **uma vez** por inquilino.

1. Caso seja necessário, instale o [Azure PowerShell](/powershell/azure/install-az-ps) no PowerShell no seu computador local.

1. No PowerShell, execute o seguinte comando:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Grant Azure Front Door acesso ao seu cofre chave
 
Conceda permissão à Porta Frontal Azure para aceder aos certificados na sua conta Azure Key Vault.

1. Na sua conta de cofre chave, em DEFINIÇÕES, selecione **as políticas de acesso**. Em seguida, **selecione Adicionar novo** para criar uma nova política.

1. Em **Select principal**, procure por **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, e escolha ** Microsoft.AzureFrontDoor-Cdn**. Clique em **Selecionar**.

1. Em **permissões secretas,** selecione **Obter** para permitir que a Porta Frontal recupere o certificado.

1. Nas **permissões de Certificado,** selecione **Obter** para permitir que a Porta Frontal recupere o certificado.

1. Selecione **OK**. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Selecione o certificado para Azure Front Door para implementar
 
1. Volte ao seu Azure Front Door Standard/Premium no portal.

1. Navegue para **segredos** em *Definições* e selecione **adicionar certificado**.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Screenshot da página de aterragem secreta da Porta frontal de Azure.":::

1. Na página de **certificado Adicionar,** selecione a caixa de verificação para o certificado que pretende adicionar ao Azure Front Door Standard/Premium. Deixe a seleção da versão como "Mais recente" e **selecione Adicionar**. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Screenshot da página de certificado de adicionar.":::

1. Uma vez que o certificado é a provisionado com sucesso, você pode usá-lo quando adicionar um novo domínio personalizado.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Screenshot do certificado adicionado com sucesso aos segredos.":::

1. Navegue para **Domínios** em *Definição* e selecione **+ Adicione** para adicionar um novo domínio personalizado. Na página **de domínio Adicionar,** escolha "Traga o seu próprio certificado (BYOC)" para *HTTPS*. Para *Secret,* selecione o certificado que pretende utilizar a partir do drop-down. 

    > [!NOTE]
    > O certificado selecionado deve ter um nome comum (CN) o mesmo que o domínio personalizado a ser adicionado.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="Screenshot de adicionar uma página de domínio personalizada com HTTPS.":::

1. Siga os passos no ecrã para validar o certificado. Em seguida, associe o domínio personalizado recém-criado a um ponto final, conforme delineado na criação de um guia [de domínio personalizado.](how-to-add-custom-domain.md)

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Change from Azure conseguiu trazer o seu próprio certificado (BYOC)

1. Pode alterar um certificado gerido pelo Azure existente para um certificado gerido pelo utilizador selecionando o estado de certificado para abrir a página de detalhes do **Certificado.**

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Screenshot do estado do certificado na página de aterragem de domínios." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. Na página de detalhes do **Certificado,** pode alterar de "Azure gerido" para "Bring Your Own Certificate (BYOC)". Em seguida, siga os mesmos passos que anteriormente para escolher um certificado. Selecione **Update** para alterar o certificado associado com um domínio.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Screenshot da página de detalhes do certificado.":::

## <a name="next-steps"></a>Passos seguintes

Saiba mais [sobre o caching com a Azure Front Door Standard/Premium](concept-caching.md).

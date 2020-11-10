---
title: Configuração detalhada para um test drive hospedado no Azure Marketplace
description: Passos de configuração de explicação para um test drive hospedado no mercado comercial
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 11/06/2020
ms.openlocfilehash: 8bc83958e4aaea5f84654a404e4afbfd60b19b0d
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415001"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Configuração detalhada para unidades de teste hospedadas

Este artigo descreve como configurar um test drive hospedado para Dynamics 365 para o Envolvimento do Cliente ou Dinâmica 365 para Operações.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Configuração para a Dinâmica 365 Envolvimento do Cliente

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/).
2. Se não conseguir aceder ao link acima, tem de submeter um pedido [aqui](https://appsource.microsoft.com/partners/list-an-app) para publicar a sua candidatura. Assim que revermos o pedido, terá acesso para iniciar o processo de publicação.
3. Encontre uma **Dinâmica 365** existente para oferta de Envolvimento com o Cliente ou crie uma nova **Oferta Dinâmica 365 para** a oferta de Envolvimento com o Cliente.
4. Selecione a **caixa de verificação de unidade de teste** e selecione um tipo de unidade de **teste** (ver bala abaixo) e, em seguida, selecione **Guardar**.

    [![Selecionando a caixa de verificação "Ativar uma unidade de teste".](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Tipo de test drive** – Escolha **o Microsoft Hosted (Dynamics 365 para o Envolvimento do Cliente & PowerApps)**. Isto indica que a Microsoft irá hospedar e manter o serviço que executa o fornecimento e desprovisionamento do utilizador do test drive.

5. Conceda ao Microsoft AppSource permissão para provisão e deprovisionar utilizadores de test drive no seu inquilino usando [estas instruções.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) Neste passo, irá gerar o ID da **App AD AZure** e os valores chave da **aplicação AD AD Azure** mencionados abaixo.
6. Preencha estes campos na página **de configuração técnica** do Test drive.

    [![A página de configuração técnica do test drive.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Unidades de teste simultâneas** max – O número de utilizadores simultâneos que podem ter um test drive ativo a funcionar ao mesmo tempo. Cada utilizador consumirá uma licença Dynamics enquanto o seu test drive estiver ativo, por isso certifique-se de que tem pelo menos estas muitas licenças Dynamics disponíveis para os utilizadores de test drive. Recomendamos 3 a 5.
    - **Duração do test drive** - O número de horas de test drive do utilizador estará ativo. Após o tempo expirado, o utilizador será desprovisionado do seu inquilino. Recomendamos 2-24 horas dependendo da complexidade da sua aplicação. O utilizador pode sempre solicitar outro test drive se ficar sem tempo e quiser aceder novamente ao test drive.
    - **URL de exemplo** – O URL para o utilizador do test drive será enviado quando iniciarem a unidade de teste. Este é tipicamente o URL da sua instância Dynamics 365 em que a sua aplicação e dados de amostra estão instalados. Valor de exemplo: `https://testdrive.crm.dynamics.com` .
    - **Exemplo web API URL** – O URL web API para a sua Instância Dinâmica 365. Recupere este valor ao iniciar sessão na sua instância Microsoft Dynamics 365 e navegar para **definir** a  >  API de Recursos de Desenvolvimento **personalizado** e copiar o  >  **Developer Resources**  >  **Instance Web API** endereço (URL). Valor de exemplo: 

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Um exemplo de Instance Web API.":::

    - **Nome de função** – O nome da função de segurança personalizada Dynamics 365 que criou para test drive ou pode utilizar um papel existente. Uma nova função deve ter privilégios mínimos necessários adicionados à função para assinar uma instância de Envolvimento do Cliente. Consulte os [privilégios mínimos necessários para iniciar seduque no Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Esta é a função que será atribuída aos utilizadores durante o seu test drive. Valor de exemplo: `testdriverole` .
    
        > [!IMPORTANT]
        > Certifique-se de que a verificação do grupo de segurança não é adicionada. Isto permite que o utilizador seja sincronizado com a instância de Envolvimento do Cliente.

    - **ID do inquilino do Azure Ative Directory** – A identificação do inquilino Azure para a sua instância Dynamics 365. Para recuperar este valor, inscreva-se no portal Azure e navegue até à **Azure Ative Directory**  >  **Properties** e copie o ID do diretório. Valor de exemplo: 172f988bf-86f1-41af-91ab-2d7cd011112341.
    - Nome do **inquilino do Azure Ative Directory** – O nome do Inquilino Azure para a sua Dynamics 365 Instance. Utilize o formato `<tenantname>.onmicrosoft.com`. Valor do exemplo: `testdrive.onmicrosoft.com` .
    - **ID da aplicação Azure Ative Directory** – O ID da app Azure Ative Directory (AD) que criou no Passo 5. Valor de exemplo: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Ative Directory application cliente secret** – Secret for the Azure AD app criado no Passo 5. Valor de exemplo: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Forneça os detalhes da listagem do mercado. Selecione **idioma** para ver os campos mais necessários.

    [![A página de detalhes da listagem do Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrição** - Uma visão geral do seu test drive. Este texto será apresentado ao utilizador enquanto o test drive estiver a ser a provisionado. Este campo suporta HTML se pretender fornecer conteúdo formatado.
    - **Manual do utilizador** – Um manual do utilizador PDF que ajuda a testar os utilizadores de unidades de condução a entender como usar a sua aplicação.
    - **Test drive demo video** – Um vídeo que mostra a sua aplicação (opcional).

## <a name="configure-for-dynamics-365-operations"></a>Configuração para a Dinâmica 365 Operações

2. Se não conseguir aceder ao link acima, tem de submeter um pedido [aqui](https://appsource.microsoft.com/partners/list-an-app) para publicar a sua candidatura. Assim que revermos o pedido, terá acesso para iniciar o processo de publicação.
3. Encontre uma oferta **de Dinâmica 365 existente para Operações** ou crie uma nova oferta **Dynamics 365 para Operações.**
4. Selecione a **caixa de verificação de unidade de teste** e selecione um tipo de unidade de **teste** (ver bala abaixo) e, em seguida, selecione **Guardar**.

    [![Selecione a caixa de verificação "Ativar uma unidade de teste".](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Tipo de test drive** – Escolha **a dinâmica 365 para** opção Operações. Isto significa que a Microsoft irá hospedar e manter o serviço que executa o fornecimento e desprovisionamento do utilizador do test drive.

5. Conceda ao Microsoft AppSource permissão para provisão e deprovisionar utilizadores de test drive no seu inquilino usando [estas instruções.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) Neste passo, irá gerar o ID da **App AD AZure** e os valores chave da **aplicação AD AD Azure** mencionados abaixo.
6. Preencha estes campos na página **de configuração técnica** do Test drive.

    [![A página de configuração técnica do Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Unidades de teste simultâneas** max – O número de utilizadores simultâneos que podem ter um test drive ativo a funcionar ao mesmo tempo. Cada utilizador consumirá uma licença Dynamics enquanto o seu test drive estiver ativo, por isso certifique-se de que tem pelo menos estas muitas licenças Dynamics disponíveis para os utilizadores de test drive. Recomendamos 3 a 5.
    - **Duração do test drive** - O número de horas de test drive do utilizador estará ativo. Após o tempo expirado, o utilizador será desprovisionado do seu inquilino. Recomendamos 2-24 horas dependendo da complexidade da sua aplicação. O utilizador pode sempre solicitar outro test drive se ficar sem tempo e quiser aceder novamente ao test drive.
    - **URL de exemplo** – O URL para o utilizador do test drive será enviado quando iniciarem a unidade de teste. Este é tipicamente o URL da sua instância Dynamics 365 em que a sua aplicação e dados de amostra estão instalados. Valor de exemplo: `https://testdrive.crm.dynamics.com` .
    - **ID do inquilino do Azure Ative Directory** – A identificação do inquilino Azure para a sua instância Dynamics 365. Para recuperar este valor, inscreva-se no portal Azure e navegue até à **Azure Ative Directory**  >  **Properties** e copie o ID do diretório. Valor de exemplo: 172f988bf-86f1-41af-91ab-2d7cd011112341.
    - Nome do **inquilino do Azure Ative Directory** – O nome do Inquilino Azure para a sua Dynamics 365 Instance. Utilize o formato `<tenantname>.onmicrosoft.com`. Valor do exemplo: `testdrive.onmicrosoft.com` .
    - **ID da aplicação Azure Ative Directory** – O ID da app Azure Ative Directory (AD) que criou no Passo 5. Valor de exemplo: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Ative Directory application cliente secret** – Secret for the Azure AD app criado no Passo 5. Valor de exemplo: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Entidade Legal do Julgamento** – Forneça uma Entidade Jurídica para atribuir um utilizador de julgamento. Pode criar um novo na [Create ou modificar uma entidade legal.](https://technet.microsoft.com/library/hh242184.aspx)
    - **Nome de função** – O nome AOT (Application Object Tree) da função de segurança personalizada Dynamics 365 que criou para o test drive. Esta é a função que será atribuída aos utilizadores durante o seu test drive.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="A página de configuração de segurança.":::

7. Forneça os detalhes da listagem do mercado. Selecione **idioma** para ver os campos mais necessários.

    [![A página de detalhes da listagem do Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Descrição** - Uma visão geral do seu test drive. Este texto será apresentado ao utilizador enquanto o test drive estiver a ser a provisionado. Este campo suporta HTML se pretender fornecer conteúdo formatado.
    - **Manual do utilizador** – Um manual do utilizador PDF que ajuda a testar os utilizadores de unidades de condução a entender como usar a sua aplicação.
    - **Test drive demo video** – Um vídeo que mostra a sua aplicação (opcional).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->

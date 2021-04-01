---
title: Comprar um nome de domínio personalizado
description: Saiba como comprar um domínio de Serviço de Aplicações e use-o como um domínio personalizado para a sua aplicação Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: cdcf22a42375949cc4d6be0b4f3062cee26219d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704859"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Buy a custom domain name for Azure App Service (Comprar um nome de domínio personalizado para o Serviço de Aplicações do Azure)

Os domínios do Serviço de Aplicações são domínios personalizados que são geridos diretamente no Azure. Facilitam a gestão de domínios personalizados para [o Azure App Service](overview.md). Este tutorial mostra-lhe como comprar um domínio de Serviço de Aplicações e atribuir nomes DNS ao Azure App Service.

Para o armazenamento Azure VM ou Azure, consulte [o domínio do Serviço de Aplicações de Atribuição ao Azure VM ou ao Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Para serviços na nuvem, consulte [configurar um nome de domínio personalizado para um serviço de nuvem Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](./index.yml) ou utilize uma aplicação que tenha criado para outro tutorial. A aplicação deve estar numa região pública do Azure. Neste momento, as Nuvens Nacionais de Azure não são apoiadas.
* [Remova o limite de gastos na sua subscrição.](../cost-management-billing/manage/spending-limit.md#remove) Não é possível comprar domínios de Serviço de Aplicações com créditos de subscrição gratuitos.

## <a name="buy-an-app-service-domain"></a>Comprar um domínio de Serviço de Aplicações

Para obter informações sobre preços nos domínios do Serviço de Aplicações, visite a [página de preços do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/) e desça até ao Domínio do Serviço de Aplicações.

1. Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

1. Na barra de pesquisa, procure e selecione **Domínios de Serviço de Aplicações.**

    ![Navegação porta-porta para domínios do Serviço de Aplicações Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na vista **'Domínios de Serviço de Aplicações',** clique em **Adicionar**.

    ![Clique em Adicionar domínios de Serviço de Aplicações](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Selecione **Click para experimentar a versão mais recente dos Domínios de Serviço de Aplicações criar experiência.**

    ![Criar domínio de Serviço de Aplicações com nova experiência](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Separador básico

1. No **separador Básicos,** configufique as definições utilizando o seguinte quadro:  

   | Definições  | Descrição |
   | -------- | ----------- |
   | **Subscrição** | A subscrição para usar para comprar o domínio. |
   | **Grupo de Recursos** | O grupo de recursos para colocar o domínio dentro Por exemplo, o grupo de recursos em que a sua aplicação se encontra. |
   | **Domínio** | Digite o domínio que quiser. Por exemplo, **contoso.com**. Se o domínio que deseja não estiver disponível, pode selecionar a partir de uma lista de sugestões de domínios disponíveis ou experimentar um domínio diferente. |

    > [!NOTE]
    > Os [seguintes domínios de nível superior](https://wikipedia.org/wiki/Top-level_domain) são suportados por domínios de Serviço de Aplicação: _com,_ _net_, _co.uk_, _org_, _nl_, _in,_ _biz_, _org.uk_, e _co.in_.
    >
    >
    
2. Quando terminar, clique em **Seguinte: Informações de contacto**.

### <a name="contact-information-tab"></a>Separador de informações de contato

1. Forneça as suas informações conforme exigido pela [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) para o registo de domínio. 

    É importante que preencha todos os campos necessários com a maior precisão possível. Dados incorretos para informações de contacto podem resultar na não compra do domínio.

1. Quando terminar, clique em **Seguinte: Avançado**.

### <a name="advanced-tab"></a>Separador avançado

1. No separador **Avançado,** configurar as definições opcionais:  

   | Definições  | Descrição |
   | -------- | ----------- |
   | **Renovação automática** | Ativado por predefinição. O seu domínio de Serviço de Aplicações está registado em incrementos de um ano. A renovação automática garante que o seu registo de domínio não expira e que mantém a propriedade do domínio. A sua subscrição Azure é automaticamente cobrada a taxa anual de registo de domínio no momento da renovação. Para excluir, **selecione Disable**. Se a renovação automática for desativada, pode [renová-la manualmente](#renew-the-domain). |
   | **Proteção de privacidade** | Ativado por predefinição. A proteção de privacidade esconde as informações de contacto do seu registo de domínio a partir da base de dados WHOIS. A proteção da privacidade já está incluída na taxa anual de registo de domínio. Para excluir, **selecione Disable**. |

2. Quando terminar, clique em **Seguinte: Tags**.

### <a name="finish"></a>Concluir

1. No separador **Tags,** desaprote as etiquetas que deseja para o seu domínio de Serviço de Aplicações. A marcação não é necessária para a utilização de domínios do Serviço de Aplicações, mas é uma [funcionalidade no Azure que o ajuda a gerir os seus recursos.](../azure-resource-manager/management/tag-resources.md)

1. Clique **em Seguinte: Revisão + criar**.

1. No **separador 'Rever +' criar,** rever a sua ordem de domínio. Quando terminar, clique em **Criar**.

    > [!NOTE]
    > Os Domínios de Serviço de Aplicações utilizam o GoDaddy para o registo de domínio e o Azure DNS para hospedar os domínios. Além da taxa anual de registo de domínio, aplicam-se os custos de utilização do Azure DNS. Para obter informações, consulte [o Preço do DNS Azure](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Quando o registo de domínio estiver concluído, vê-se um botão **De ir ao** recurso. Selecione-o para ver a sua página de gestão.

    ![Domínio do Serviço de Aplicações criado. Ir para recurso](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

Está agora pronto para atribuir uma aplicação de Serviço de Aplicações a este domínio personalizado.

## <a name="prepare-the-app"></a>Preparar a aplicação

Para mapear um nome DNS personalizado para uma aplicação web, o plano de Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) da web deve ser um nível pago (Compartilhado, Básico, Padrão, Premium ou Consumo para Funções Azure). Neste passo, vai confirmar que a aplicação do Serviço de Aplicações está no escalão de preço suportado.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para a aplicação no portal do Azure

1. A partir da barra de pesquisa superior, procure e selecione **Serviços de Aplicações.**

    ![Pesquisa de Serviços de Aplicações](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selecione o nome da aplicação.

    ![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

1. No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)**.

    ![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. O escalão atual da aplicação é realçado com um limite azul. Confirme que a aplicação não está no escalão **F1**. O DNS personalizado não é suportado no escalão **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Screenshot do menu de navegação à esquerda da página da aplicação com Scale up (plano de Serviço de Aplicações) selecionado.":::

1. Se o plano de Serviço de Aplicações não estiver no nível **F1,** feche a página **Scale up** e salte para Comprar [o domínio](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

1. Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

1. Clique em **Aplicar**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Screenshot dos níveis de preços de domínio personalizados na categoria Produção com o separador De Produção, plano B1 e o botão Apply realçado.":::

    Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

    ![Confirmação de operação de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Map App Service Domain para a sua app

É fácil mapear um nome de anfitrião no seu domínio de Serviço de Aplicações para uma aplicação de Serviço de Aplicações, desde que esteja na mesma subscrição. Mapeia o domínio do Serviço de Aplicações ou qualquer um dos seus subdomínios diretamente na sua aplicação, e o Azure cria os registos DNS necessários para si.

> [!NOTE]
> Se o domínio e a aplicação estiverem em diferentes subscrições, mapeia o domínio do Serviço de Aplicações para a aplicação tal como [mapear um domínio comprado externamente.](app-service-web-tutorial-custom-domain.md) Neste caso, o Azure DNS é o fornecedor de domínio externo, e é necessário [adicionar manualmente os registos DNS necessários](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mapear o domínio

1. Na navegação à esquerda da página da aplicação, percorra a secção **Definições** e selecione **domínios personalizados**.

    ![Screenshot que mostra o menu de domínios personalizados.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **Selecione Adicionar domínio personalizado**.

    ![Screenshot que mostra o item do nome do anfitrião Adicionar.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digite o domínio do Serviço de Aplicações (como **contoso.com)** ou um subdomínio (como **www.contoso.com)** e clique em **Validar**.

    > [!NOTE]
    > Se fez um erro no nome de domínio do Serviço de Aplicações, aparece na parte inferior da página um erro de verificação para lhe dizer que está a perder alguns registos DNS. Não é necessário adicionar estes registos manualmente para um domínio do Serviço de Aplicações. Certifique-se apenas de que escreve corretamente o nome do domínio e clique em **Validar** novamente.
    >
    > ![Screenshot que mostra um erro de verificação.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Aceite o **tipo de gravação do nome anfitrião** e clique em Adicionar domínio **personalizado**.

    ![Screenshot que mostra o botão de domínio personalizado Adicionar.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. Pode levar algum tempo para que o novo domínio personalizado seja refletido na página **de Domínios Personalizados** da aplicação. Atualize o navegador para atualizar os dados.

    ![Screenshot que mostra a adição do disco CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está vinculado a um certificado TLS/SSL. Qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para adicionar uma ligação TLS, consulte [Secure um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Testar o domínio personalizado

Para testar o domínio personalizado, navegue no seu navegador.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio do Serviço de Aplicações que comprou é válido por um ano a partir do momento da compra. Por padrão, o domínio está configurado para renovar automaticamente cobrando o seu método de pagamento para o próximo ano. Pode renovar manualmente o seu nome de domínio.

Se pretender desligar a renovação automática ou se pretender renovar manualmente o seu domínio, siga os passos aqui.

1. Na barra de pesquisa, procure e selecione **Domínios de Serviço de Aplicações.**

    ![Navegação porta-porta para domínios do Serviço de Aplicações Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na secção **Domínios de Serviço de Aplicações,** selecione o domínio que pretende configurar.

1. A partir da navegação à esquerda do domínio, selecione **a renovação do domínio**. Para parar de renovar automaticamente o seu domínio, selecione **Off**. A regulação entra em vigor imediatamente.

    ![Screenshot que mostra a opção de renovar automaticamente o seu domínio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Ao navegar para longe da página, ignore o erro "As suas edições não salvas serão descartadas" clicando **OK**.
    >

Para renovar manualmente o seu domínio, selecione **Renovar o domínio**. No entanto, este botão só está ativo [90 dias antes da expiração do domínio](#when-domain-expires).

Se a renovação do seu domínio for bem sucedida, receberá uma notificação por e-mail dentro de 24 horas.

## <a name="when-domain-expires"></a>Quando o domínio expira

A Azure lida com domínios de Serviço de Aplicações expirados ou expirados da seguinte forma:

* Se a renovação automática for desativada: 90 dias antes da expiração do domínio, é enviado um e-mail de notificação de renovação e o botão **de domínio Renovar** é ativado no portal.
* Se estiver ativada a renovação automática: No dia a seguir à data de validade do seu domínio, o Azure tenta cobrar-lhe a renovação do nome de domínio.
* Se ocorrer um erro durante a renovação automática (por exemplo, o seu cartão no ficheiro está expirado), ou se a renovação automática for desativada e permitir que o domínio expire, o Azure notifica-o da expiração do domínio e estaciona o nome do seu domínio. Pode [renovar manualmente](#renew-the-domain) o seu domínio.
* Nos dias 4 e 12 dias após a expiração, a Azure envia-lhe e-mails de notificação adicionais. Pode [renovar manualmente](#renew-the-domain) o seu domínio. No quinto dia após a expiração, a resolução de DNS para para o domínio expirado.
* No 19º dia após a expiração, o seu domínio permanece em espera, mas fica sujeito a uma taxa de resgate. Pode ligar para o suporte ao cliente para renovar o seu nome de domínio, sujeito a quaisquer taxas de renovação e resgate aplicáveis.
* No 25º dia após a expiração, a Azure coloca o seu domínio em leilão com um serviço de leilão da indústria de nome de domínio. Pode ligar para o suporte ao cliente para renovar o seu nome de domínio, sujeito a quaisquer taxas de renovação e resgate aplicáveis.
* No 30º dia após a expiração, já não és capaz de resgatar o teu domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerir registos DNS personalizados

Em Azure, os registos DNS para um Domínio de Serviço de Aplicações são geridos através do [Azure DNS](https://azure.microsoft.com/services/dns/). Pode adicionar, remover e atualizar registos DNS, tal como para um domínio comprado externamente. Para gerir registos DNS personalizados:

1. Na barra de pesquisa, procure e selecione **Domínios de Serviço de Aplicações.**

    ![Navegação porta-porta para domínios do Serviço de Aplicações Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na secção **Domínios de Serviço de Aplicações,** selecione o domínio que pretende configurar.

1. Na página **'Vista Geral',** selecione **Gerir os registos DNS**.

    ![Screenshot que mostra onde aceder aos registos DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Para obter informações sobre como editar registos DNS, consulte [Como gerir as Zonas DNS no portal Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de adquirir o Domínio do Serviço de Aplicações, tem cinco dias para cancelar a sua compra para um reembolso total. Após cinco dias, pode eliminar o Domínio do Serviço de Aplicações, mas não pode receber um reembolso.

1. Na barra de pesquisa, procure e selecione **Domínios de Serviço de Aplicações.**

    ![Navegação porta-porta para domínios do Serviço de Aplicações Azure](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Na secção **Domínios de Serviço de Aplicações,** selecione o domínio que pretende configurar.

1. Na navegação à esquerda do domínio, selecione **as ligações hostname**. As ligações de nome anfitrião de todos os serviços da Azure estão listadas aqui.

    ![Screenshot que mostra a página de encadernações do nome anfitrião.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Elimine cada ligação de nome de anfitrião selecionando **...**  >  **Eliminar**. Depois de todas as ligações serem eliminadas, **selecione Save**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. Na navegação à esquerda do domínio, selecione **Overview**. 

1. Se o período de cancelamento do domínio adquirido não tiver decorrido, selecione **Cancelar a compra**. Caso contrário, vê-se um botão **Eliminar.** Para eliminar o domínio sem reembolso, selecione **Delete**.

    ![Screenshot que mostra onde apagar ou cancelar um domínio adquirido.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Confirme a operação selecionando **Sim**.

    Após a operação estar concluída, o domínio é libertado da sua subscrição e disponível para qualquer pessoa comprar novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar o URL predefinido para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Direcioná-los para uma subdiretório, `public` como, ver [Redirecionar para um diretório personalizado](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Passos seguintes

Saiba como ligar um certificado TLS/SSL personalizado ao Serviço de Aplicações.

> [!div class="nextstepaction"]
> [Proteja um nome DNS personalizado com uma ligação TLS no Azure App Service](configure-ssl-bindings.md)

---
title: Compre um nome de domínio personalizado
description: Aprenda a comprar um domínio de Serviço de Aplicações e use-o como um domínio personalizado para o seu serviço de aplicações Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: afb40d0f3681bc02351e43166fccfaafe7741128
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259282"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Compre um nome de domínio personalizado para o Azure App Service

Os domínios do Serviço de Aplicações são domínios de alto nível que são geridos diretamente no Azure. Facilitam a gestão de domínios personalizados para o [Azure App Service.](overview.md) Este tutorial mostra-lhe como comprar um domínio de Serviço de Aplicações e atribuir nomes de DNS ao Serviço de Aplicações Azure.

Para armazenamento Azure VM ou Azure, consulte o [domínio de Serviço de Atribuição de Aplicações para O Armazenamento Azure VM ou Azure](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Para serviços de nuvem, consulte [Configurar um nome de domínio personalizado para um serviço](../cloud-services/cloud-services-custom-domain-name-portal.md)de nuvem Azure .

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](/azure/app-service/) ou utilize uma aplicação que tenha criado para outro tutorial.
* [Remova o limite de gastos na sua subscrição](../cost-management-billing/manage/spending-limit.md#remove). Não é possível comprar domínios do Serviço app com créditos de subscrição gratuitos.

## <a name="prepare-the-app"></a>Preparar a aplicação

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Para utilizar domínios personalizados no Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) Azure, o plano de Serviço de Aplicações da sua aplicação deve ser um nível pago (**Shared,** **Basic,** **Standard**ou **Premium).** Neste passo, certifique-se de que a aplicação está no nível de preços suportado.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para a aplicação no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicações** e selecione o nome da aplicação Web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)** .

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Confirme que a aplicação não está no escalão **F1**. O DNS personalizado não é suportado no escalão **F1**. 

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano de Serviço de Aplicações não estiver no nível **F1,** feche a página **Scale up** e salte para Comprar [o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Confirmação de operação de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar o domínio

### <a name="pricing-information"></a>Informação sobre preços
Para obter informações sobre os domínios do serviço de aplicações do Azure, visite a página de preços do serviço de [aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/) e desloque-se até ao Domínio do Serviço de Aplicações.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Abra ao [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.

### <a name="launch-buy-domains"></a>Domínios de compra de lançamento
No separador Serviços de **Aplicações,** clique no nome da sua aplicação, selecione **Definições**e, em seguida, selecione **domínios Personalizados**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na página **de domínios Personalizados,** clique em **Comprar Domínio**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Se não conseguir ver a secção domínios do serviço de **aplicações,** tem de remover o limite de gastos na sua conta Azure (ver [Pré-requisitos).](#prerequisites)
>
>

### <a name="configure-the-domain-purchase"></a>Configure a compra de domínio

Na página de Domínio do Serviço de **Aplicações,** na caixa **de pesquisa de domínio,** escreva o nome de domínio que pretende comprar e digite `Enter`. Os domínios disponíveis sugeridos são mostrados logo abaixo da caixa de texto. Selecione um ou mais domínios que pretende comprar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Os [seguintes domínios de alto nível](https://wikipedia.org/wiki/Top-level_domain) são suportados por domínios do Serviço de Aplicações: _com,_ _net_, _co.uk_, _org_, nl , _in,_ _biz,_ _org.uk_, e _co.in_.
>
>

Clique nas **Informações** de Contacto e preencha o formulário de informação de contacto do domínio. Quando terminar, clique em **OK** para voltar à página de Domínio do Serviço de Aplicações.

É importante que preencha todos os campos necessários com a maior precisão possível. Dados incorretos para informações de contacto podem resultar na não compra de domínios.

Em seguida, selecione as opções desejadas para o seu domínio. Consulte a tabela seguinte para obter explicações:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Proteção da privacidade | Ativar | Opte pela "Proteção da Privacidade", que está incluída no preço de compra _gratuitamente._ Alguns domínios de alto nível são geridos por registradores que não suportam a proteção da privacidade, e estão listados na página de **proteção da Privacidade.** |
| Atribuir nomes de anfitriões padrão | **www** e **\@** | Selecione as encadernações de nome de anfitrião desejadas, se desejar. Quando a operação de compra de domínio estiver concluída, a sua aplicação pode ser acedida nos nomes de anfitriões selecionados. Se a aplicação estiver por trás do [Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)não vê a opção de atribuir o domínio raiz (@), porque o Gestor de Tráfego não suporta registos A. Pode efazer alterações nas atribuições de nome de anfitrião após a compra do domínio estar concluída. |

### <a name="accept-terms-and-purchase"></a>Aceitar termos e comprar

Clique em **Termos Legais** para rever os termos e encargos e, em seguida, clique em **Comprar**.

> [!NOTE]
> Os domínios do serviço de aplicações usam o GoDaddy para o registo de domínioe o Azure DNS para acolher os domínios. Além da taxa de registo de domínio, aplicam-se taxas de utilização para o DNS Azure. Para obter informações, consulte [o Preço do DNS Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

De volta à página de Domínio do Serviço de **Aplicações,** **clique**ok . Enquanto a operação está em curso, vê as seguintes notificações:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Teste os nomes de anfitriões

Se atribuiu nomes de anfitriões padrão à sua aplicação, também vê uma notificação de sucesso para cada nome de anfitrião selecionado.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Também vê os nomes de anfitriões selecionados na página de **domínios Personalizados,** na secção Nomes de **Anfitriões Personalizados.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está ligado a um certificado SSL, e qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para configurar a ligação SSL, consulte [Proteja um nome DNS personalizado com uma ligação SSL no Serviço de Aplicações Azure](configure-ssl-bindings.md).
>

Para testar os nomes de anfitriões, navegue para os nomes de anfitriões listados no navegador. No exemplo da imagem anterior, tente navegar para _kontoso.net_ e _www\.kontoso.net._

## <a name="assign-hostnames-to-app"></a>Atribuir nomes de anfitriões para app

Se optar por não atribuir um ou mais nomes de anfitriões predefinidos à sua aplicação durante o processo de compra, ou se precisar de atribuir um nome de anfitrião não listado, pode atribuir um nome de anfitrião a qualquer momento.

Também pode atribuir nomes de anfitriões no Domínio do Serviço de Aplicações a qualquer outra aplicação. Os passos dependem se o Domínio do Serviço de Aplicações e a aplicação pertencem à mesma subscrição.

- Subscrição diferente: Map estão os registos DNS personalizados do Domínio do Serviço de Aplicações para a aplicação como um domínio comprado externamente. Para obter informações sobre a adição de nomes dNS personalizados a um domínio de serviço de aplicações, consulte [Gerir registos DNS personalizados](#custom). Para mapear um domínio comprado externo para uma aplicação, consulte [mapeie um nome DNS personalizado existente para o Serviço de Aplicações Azure](app-service-web-tutorial-custom-domain.md). 
- Mesma subscrição: Utilize os seguintes passos.

### <a name="launch-add-hostname"></a>Lançamento adicionar nome de anfitrião
Na página de Serviços de **Aplicações,** selecione o nome da sua aplicação para a a sua designação de nomes de anfitriões, selecione **Definições**e, em seguida, selecione **domínios Personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Certifique-se de que o seu domínio adquirido está listado na secção domínios do serviço de **aplicações,** mas não o selecione. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Todos os Domínios do Serviço de Aplicações na mesma subscrição são mostrados na página de **domínios Personalizados** da aplicação. Se o seu domínio estiver na subscrição da aplicação, mas não o consegue ver na página de **domínios Personalizados** da aplicação, tente reabrir a página de **domínios Personalizados** ou atualizar a página web. Verifique também o sino de notificação no topo do portal Azure para obter falhas de progresso ou criação.
>
>

Selecione **Adicionar nome de anfitrião**.

### <a name="configure-hostname"></a>Configurar o nome de anfitrião
No diálogo **Add hostname,** escreva o nome de domínio totalmente qualificado do seu Domínio de Serviço de Aplicações ou qualquer subdomínio. Por exemplo:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Quando terminar, selecione **Validar**. O tipo de registo de nome de anfitrião é automaticamente selecionado para si.

Selecione **Adicionar nome de anfitrião**.

Quando a operação estiver concluída, consulte uma notificação de sucesso para o nome de anfitrião atribuído.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Fechar adicionar nome de anfitrião
Na página Adicionar nome de **anfitrião,** atribua qualquer outro nome de anfitrião à sua aplicação, conforme desejado. Quando terminar, feche a página Adicionar nome **de anfitrião.**

Deverá agora ver o nome de anfitrião recentemente atribuído na página de **domínios Personalizados** da sua aplicação.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Teste os nomes de anfitriões

Navegue para os nomes de anfitriões listados no navegador. No exemplo da imagem anterior, tente navegar para _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio do Serviço de Aplicações que comprou é válido por um ano a partir do momento da compra. Por predefinição, o domínio está configurado para renovar automaticamente, cobrando o seu método de pagamento para o próximo ano. Pode renovar manualmente o seu nome de domínio.

Se quiser desativar a renovação automática, ou se quiser renovar manualmente o seu domínio, siga os passos aqui.

No separador Serviços de **Aplicações,** clique no nome da sua aplicação, selecione **Definições**e, em seguida, selecione **domínios Personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na secção Domínios do **Serviço de Aplicações,** selecione o domínio que pretende configurar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

A partir da navegação esquerda do domínio, selecione renovação de **domínio.** Para parar de renovar automaticamente o seu domínio, selecione **Off**, e, em seguida, **Guardar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Para renovar manualmente o seu domínio, selecione Renovar o **domínio**. No entanto, este botão só está ativo [90 dias antes da expiração do domínio](#when-domain-expires).

Se a renovação do seu domínio for bem sucedida, receberá uma notificação por e-mail dentro de 24 horas.

## <a name="when-domain-expires"></a>Quando o domínio expirar

O Azure lida com domínios de Serviço de Aplicações expirados ou expirados da seguinte forma:

* Se a renovação automática for desativada: 90 dias antes da expiração do domínio, é enviado um e-mail de notificação de renovação para si e o botão **de domínio Renovar** é ativado no portal.
* Se a renovação automática estiver ativada: No dia seguinte à data de validade do seu domínio, o Azure tenta cobrar-lhe a renovação do nome de domínio.
* Se ocorrer um erro durante a renovação automática (por exemplo, o seu cartão no ficheiro expira), ou se a renovação automática for desativada e permitir que o domínio expire, o Azure notifica-o da expiração do domínio e estaciona o seu nome de domínio. Pode [renovar manualmente](#renew-the-domain) o seu domínio.
* Nos dias 4 e 12 dias após a expiração, o Azure envia-lhe e-mails de notificação adicionais. Pode [renovar manualmente](#renew-the-domain) o seu domínio.
* No 19º dia após a expiração, o seu domínio permanece em espera, mas fica sujeito a uma taxa de resgate. Pode ligar para o apoio ao cliente para renovar o seu nome de domínio, sujeito a quaisquer taxas de renovação e de resgate aplicáveis.
* No 25º dia após a expiração, o Azure coloca o seu domínio em leilão com um serviço de leilões da indústria de nome de domínio. Pode ligar para o apoio ao cliente para renovar o seu nome de domínio, sujeito a quaisquer taxas de renovação e de resgate aplicáveis.
* No 30º dia após a expiração, já não és capaz de redimir o teu domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerir registos DNS personalizados

Em Azure, os registos DNS para um Domínio de Serviço de Aplicações são geridos utilizando [O DNS Azure](https://azure.microsoft.com/services/dns/). Pode adicionar, remover e atualizar os registos de DNS, tal como para um domínio comprado externamente.

### <a name="open-app-service-domain"></a>Domínio de serviço de aplicativo aberto

No portal Azure, a partir do menu esquerdo, selecione **Todos os serviços** > Domínios de Serviço de **Aplicações.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio para gerir. 

### <a name="access-dns-zone"></a>Zona DNS de acesso

No menu esquerdo do domínio, selecione **a zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Esta ação abre a página da [zona DNS](../dns/dns-zones-records.md) do seu Domínio de Serviço de Aplicações em DNS Azure. Para obter informações sobre como editar registos DNS, consulte [como gerir as Zonas DNS no portal Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de adquirir o Domínio do Serviço de Aplicações, tem cinco dias para cancelar a sua compra para um reembolso total. Após cinco dias, pode eliminar o Domínio do Serviço de Aplicações, mas não pode receber um reembolso.

### <a name="open-app-service-domain"></a>Domínio de serviço de aplicativo aberto

No portal Azure, a partir do menu esquerdo, selecione **Todos os serviços** > Domínios de Serviço de **Aplicações.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio para que deseja cancelar ou eliminar. 

### <a name="delete-hostname-bindings"></a>Eliminar encadernações de nome de anfitrião

No menu esquerdo do domínio, selecione **ligações hostname**. As encadernações de nome de anfitrião de todos os serviços azure estão listadas aqui.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Não é possível eliminar o Domínio do Serviço de Aplicações até que todas as ligações de nome de anfitrião sejam eliminadas.

Elimine cada encadernação de nome de anfitrião selecionando **...**  > **Apagar**. Depois de todas as ligações serem eliminadas, selecione **Guardar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Cancelar ou excluir

No menu esquerdo do domínio, selecione **Visão Geral**. 

Se o período de cancelamento no domínio adquirido não tiver decorrido, **selecione Cancelar a compra**. Caso contrário, vê um botão **Delete.** Para eliminar o domínio sem reembolso, selecione **Eliminar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Para confirmar a operação, selecione **Sim**.

Após a operação estar concluída, o domínio é liberado a partir da sua subscrição e disponível para que qualquer pessoa volte a comprar. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar o URL predefinido para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Para direcioná-los para um subdiretório, como `public`, consulte [o Direct predefinido URL para um diretório personalizado](app-service-web-tutorial-custom-domain.md#virtualdir).

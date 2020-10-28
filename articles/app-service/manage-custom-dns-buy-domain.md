---
title: Comprar um nome de domínio personalizado
description: Saiba como comprar um domínio de Serviço de Aplicações e use-o como um domínio personalizado para a sua aplicação Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: f09ef109be68dbdb6c82ce5fc89761b10bec85f3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781690"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Buy a custom domain name for Azure App Service (Comprar um nome de domínio personalizado para o Serviço de Aplicações do Azure)

Os domínios do Serviço de Aplicações são domínios de alto nível que são geridos diretamente no Azure. Facilitam a gestão de domínios personalizados para [o Azure App Service](overview.md). Este tutorial mostra-lhe como comprar um domínio de Serviço de Aplicações e atribuir nomes DNS ao Azure App Service.

Para o armazenamento Azure VM ou Azure, consulte [o domínio do Serviço de Aplicações de Atribuição ao Azure VM ou ao Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Para serviços na nuvem, consulte [configurar um nome de domínio personalizado para um serviço de nuvem Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](./index.yml) ou utilize uma aplicação que tenha criado para outro tutorial.
* [Remova o limite de gastos na sua subscrição.](../cost-management-billing/manage/spending-limit.md#remove) Não é possível comprar domínios de Serviço de Aplicações com créditos de subscrição gratuitos.
* Um Serviço de Aplicações numa Região Pública Azure. Por favor, note neste momento, as Nuvens Nacionais do Azure não são apoiadas.

## <a name="prepare-the-app"></a>Preparar a aplicação

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Para utilizar domínios personalizados no Azure App Service, o plano de Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/) da sua aplicação deve ser um nível pago **(Partilhado,** **Básico,** **Padrão** ou **Premium).** Neste passo, certifique-se de que a aplicação está no nível de preços suportados.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para a aplicação no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicações** e selecione o nome da aplicação Web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)** .

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Confirme que a aplicação não está no escalão **F1** . O DNS personalizado não é suportado no escalão **F1** . 

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Screenshot do menu de navegação à esquerda da página da aplicação com Scale up (plano de Serviço de Aplicações) selecionado.":::

Se o plano de Serviço de Aplicações não estiver no nível **F1,** feche a página **Scale up** e salte para Comprar [o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Selecione qualquer uma das camadas não gratuitas ( **D1** , **B1** , **B2** , **B3** ou qualquer camada na categoria **Produção** ). Para obter opções adicionais, clique em **Ver opções adicionais** .

Clique em **Aplicar** .

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Screenshot do menu de navegação à esquerda da página da aplicação com Scale up (plano de Serviço de Aplicações) selecionado.":::

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Confirmação de operação de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar o domínio

### <a name="pricing-information"></a>Informação sobre preços
Para obter informações sobre preços nos domínios do Serviço de Aplicações Azure, visite a página de preços do [Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/) e desça até ao Domínio do Serviço de Aplicações.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Abra ao [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.

### <a name="launch-buy-domains"></a>Lançar domínios de compra
No separador Serviços de **Aplicações,** clique no nome da sua aplicação, selecione **Definições** e, em seguida, selecione **domínios personalizados**
   
![Screenshot que mostra domínios personalizados em destaque.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na página **de domínios personalizados,** clique em **Comprar Domínio.**

![Screenshot que mostra Buy Domain em destaque.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Se não conseguir ver a secção Domínios do Serviço de **Aplicações,** tem de remover o limite de gastos na sua conta Azure (ver [Pré-Requisitos).](#prerequisites)
>
>

### <a name="configure-the-domain-purchase"></a>Configure a compra de domínio

Na página **'Domínio do Serviço de Aplicações',** na caixa **de pesquisa de domínio,** digite o nome de domínio que pretende comprar e escreva `Enter` . Os domínios disponíveis sugeridos são mostrados logo abaixo da caixa de texto. Selecione um ou mais domínios que pretende comprar.

![Screenshot que mostra a caixa de pesquisa de domínio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Os [seguintes domínios de nível superior](https://wikipedia.org/wiki/Top-level_domain) são suportados por domínios de Serviço de Aplicação: _com,_ _net_ , _co.uk_ , _org_ , _nl_ , _in,_ _biz_ , _org.uk_ , e _co.in_ .
>
>

Clique na **Informação de Contacto** e preencha o formulário de informação de contacto do domínio. Quando terminar, clique **em OK** para voltar à página de Domínio do Serviço de Aplicações.

É importante que preencha todos os campos necessários com a maior precisão possível. Dados incorretos para informações de contacto podem resultar em falha na compra de domínios.

Em seguida, selecione as opções desejadas para o seu domínio. Consulte a tabela seguinte para obter explicações:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Proteção de privacidade | Ativar | Opte pela "Proteção da Privacidade", que está incluída no preço de compra _gratuitamente._ Alguns domínios de alto nível são geridos por registradores que não suportam a proteção da privacidade, e estão listados na página **de proteção de privacidade.** |
| Atribuir nomes de anfitriões predefinidos | **www.****\@** | Selecione as ligações de nome de anfitrião desejadas, se desejar. Quando a operação de compra de domínio estiver concluída, a sua aplicação pode ser acedida nos anfitriões selecionados. Se a aplicação estiver por trás [do Azure Traffic Manager,](https://azure.microsoft.com/services/traffic-manager/)não vê a opção de atribuir o domínio raiz (@), porque o Traffic Manager não suporta registos A. Pode efetuar alterações nas atribuições do nome anfitrião após a conclusão da compra de domínio. |

### <a name="accept-terms-and-purchase"></a>Aceitar termos e compra

Clique **em Termos Legais** para rever os termos e encargos e, em seguida, clique em **Comprar** .

> [!NOTE]
> Os Domínios de Serviço de Aplicações utilizam o GoDaddy para o registo de domínio e o Azure DNS para hospedar os domínios. Além da taxa de registo de domínio, aplicam-se os custos de utilização do Azure DNS. Para obter informações, consulte [o Preço do DNS Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

De volta à página de Domínio do Serviço de **Aplicações,** clique **em OK** . Enquanto a operação está em curso, vê as seguintes notificações:

![Screenshot que mostra a validação em curso mensagem.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![Screenshot que mostra a notificação de que a compra foi bem sucedida.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Teste os hostnames

Se atribuiu nomes de anfitriões predefinidos à sua aplicação, também vê uma notificação de sucesso para cada nome de anfitrião selecionado.

![Screenshot que mostra a notificação de sucesso para cada nome de anfitrião selecionado.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Também vê os hostnames selecionados na página **de domínios personalizados,** na secção **Hostnames personalizados.**

![Screenshot que mostra os hostnames selecionados na secção Hostnames personalizados da página de domínios personalizados.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Uma etiqueta **Não Segura** para o seu domínio personalizado significa que ainda não está vinculado a um certificado TLS/SSL, e qualquer pedido HTTPS de um navegador para o seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para configurar a ligação TLS, consulte [Secure a custom DNS name with a TLS/SSL binding in Azure App Service](configure-ssl-bindings.md).
>

Para testar os números hospedeiros, navegue para os anfitriões listados no navegador. No exemplo da imagem anterior, tente navegar para _kontoso.net_ e _www \. kontoso.net_ .

## <a name="assign-hostnames-to-app"></a>Atribuir os hostnames à app

Se optar por não atribuir um ou mais nomes de anfitriões predefinidos à sua aplicação durante o processo de compra, ou se precisar de atribuir um nome de anfitrião não listado, pode atribuir um nome de anfitrião a qualquer momento.

Também pode atribuir osamames de anfitriões no Domínio do Serviço de Aplicações a qualquer outra aplicação. Os passos dependem se o Domínio do Serviço de Aplicações e a aplicação pertencem à mesma subscrição.

- Subscrição diferente: Mapear registos DNS personalizados do Domínio do Serviço de Aplicações para a aplicação como um domínio comprado externamente. Para obter informações sobre a adição de nomes DNS personalizados a um Domínio de Serviço de Aplicações, consulte [Gerir registos DNS personalizados](#custom). Para mapear um domínio comprado externo para uma aplicação, consulte [mapear um nome DNS personalizado existente para o Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Mesma subscrição: Utilize os seguintes passos.

### <a name="launch-add-hostname"></a>Lançamento adicionar nome de anfitrião
Na página Serviços de **Aplicações,** selecione o nome da sua aplicação para a a que pretende atribuir nomes de anfitriões, selecione **Definições** e, em seguida, selecione **domínios personalizados** .

![Screenshot que mostra domínios personalizados em destaque.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Certifique-se de que o seu domínio adquirido está listado na secção **Domínios do Serviço de Aplicações,** mas não o selecione. 

![Screenshot que mostra o seu domínio adquirido na secção Domínios do Serviço de Aplicações.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Todos os domínios de serviço de aplicações na mesma subscrição são mostrados na página de **domínios personalizados** da aplicação. Se o seu domínio estiver na subscrição da aplicação, mas não conseguir vê-lo na página de **domínios personalizados** da aplicação, tente reabrir a página **de domínios personalizados** ou refrescar a página web. Além disso, consulte o sino de notificação no topo do portal Azure para obter falhas de progresso ou criação.
>
>

Selecione **Adicionar nome de anfitrião** .

### <a name="configure-hostname"></a>Configure o nome de anfitrião
No diálogo **de nome anfitrião Adicionar,** digite o nome de domínio totalmente qualificado do seu Domínio de Serviço de Aplicações ou qualquer subdomínio. Por exemplo:

- kontoso.net
- www \. kontoso.net
- abc.kontoso.net

Quando terminar, **selecione Validate** . O tipo de registo de nome anfitrião é selecionado automaticamente para si.

Selecione **Adicionar nome de anfitrião** .

Quando a operação estiver concluída, vê uma notificação de sucesso para o nome de anfitrião atribuído.  

![Screenshot que mostra a notificação de sucesso para o nome de anfitrião atribuído.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Adicionar nome de anfitrião
Na página **'Adicionar nome de anfitrião',** atribua qualquer outro nome de anfitrião à sua aplicação, conforme pretendido. Quando terminar, feche a página **de nome de anfitrião.**

Deverá agora ver o novo nome de anfitrião na página de **domínios personalizados** da sua aplicação.

![Screenshot que mostra os novos nomeados anfitriões na página de domínios personalizados da sua aplicação.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Teste os hostnames

Navegue para os anfitriões listados no navegador. No exemplo da imagem anterior, tente navegar para _abc.kontoso.net_ .

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio do Serviço de Aplicações que comprou é válido por um ano a partir do momento da compra. Por padrão, o domínio está configurado para renovar automaticamente cobrando o seu método de pagamento para o próximo ano. Pode renovar manualmente o seu nome de domínio.

Se pretender desligar a renovação automática ou se pretender renovar manualmente o seu domínio, siga os passos aqui.

No separador Serviços de **Aplicação,** clique no nome da sua aplicação, selecione **Definições** e, em seguida, selecione **domínios personalizados** .

![Screenshot que mostra domínios personalizados em destaque.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na secção **Domínios de Serviço de Aplicações,** selecione o domínio que pretende configurar.

![Screenshot que mostra o seu domínio adquirido na secção Domínios do Serviço de Aplicações.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

A partir da navegação à esquerda do domínio, selecione **a renovação do domínio** . Para parar de renovar o seu domínio automaticamente, selecione **Off** e, em seguida, **Guarde** .

![Screenshot que mostra a opção de renovar automaticamente o seu domínio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Para renovar manualmente o seu domínio, selecione **Renovar o domínio** . No entanto, este botão só está ativo [90 dias antes da expiração do domínio](#when-domain-expires).

Se a renovação do seu domínio for bem sucedida, receberá uma notificação por e-mail dentro de 24 horas.

## <a name="when-domain-expires"></a>Quando o domínio expira

A Azure lida com domínios de Serviço de Aplicações expirados ou expirados da seguinte forma:

* Se a renovação automática for desativada: 90 dias antes da expiração do domínio, é enviado um e-mail de notificação de renovação e o botão **de domínio Renovar** é ativado no portal.
* Se estiver ativada a renovação automática: No dia a seguir à data de validade do seu domínio, o Azure tenta cobrar-lhe a renovação do nome de domínio.
* Se ocorrer um erro durante a renovação automática (por exemplo, o seu cartão no ficheiro está expirado), ou se a renovação automática for desativada e permitir que o domínio expire, o Azure notifica-o da expiração do domínio e estaciona o nome do seu domínio. Pode [renovar manualmente](#renew-the-domain) o seu domínio.
* Nos dias 4 e 12 dias após a expiração, a Azure envia-lhe e-mails de notificação adicionais. Pode [renovar manualmente](#renew-the-domain) o seu domínio.
* No 19º dia após a expiração, o seu domínio permanece em espera, mas fica sujeito a uma taxa de resgate. Pode ligar para o suporte ao cliente para renovar o seu nome de domínio, sujeito a quaisquer taxas de renovação e resgate aplicáveis.
* No 25º dia após a expiração, a Azure coloca o seu domínio em leilão com um serviço de leilão da indústria de nome de domínio. Pode ligar para o suporte ao cliente para renovar o seu nome de domínio, sujeito a quaisquer taxas de renovação e resgate aplicáveis.
* No 30º dia após a expiração, já não és capaz de resgatar o teu domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerir registos DNS personalizados

Em Azure, os registos DNS para um Domínio de Serviço de Aplicações são geridos através do [Azure DNS](https://azure.microsoft.com/services/dns/). Pode adicionar, remover e atualizar registos DNS, tal como para um domínio comprado externamente.

### <a name="open-app-service-domain"></a>Abra o domínio do serviço de aplicações

No portal Azure, a partir do menu esquerdo, selecione **Todos os**  >  **serviços De Serviço de Aplicação Domínios** .

![Screenshot que mostra onde aceder domínios de serviço de aplicações.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio para gerir. 

### <a name="access-dns-zone"></a>Aceder à zona DE DNS

No menu esquerdo do domínio, selecione **a zona DNS** .

![Screenshot que mostra onde selecionar a zona DE DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Esta ação abre a página da [zona DNS](../dns/dns-zones-records.md) do seu Domínio de Serviço de Aplicações em DNS Azure. Para obter informações sobre como editar registos DNS, consulte [Como gerir as Zonas DNS no portal Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de adquirir o Domínio do Serviço de Aplicações, tem cinco dias para cancelar a sua compra para um reembolso total. Após cinco dias, pode eliminar o Domínio do Serviço de Aplicações, mas não pode receber um reembolso.

### <a name="open-app-service-domain"></a>Abra o domínio do serviço de aplicações

No portal Azure, a partir do menu esquerdo, selecione **Todos os**  >  **serviços De Serviço de Aplicação Domínios** .

![Screenshot que mostra onde aceder domínios de serviço de aplicações.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio para que pretenda cancelar ou apagar. 

### <a name="delete-hostname-bindings"></a>Eliminar encadernações de nome de anfitrião

No menu esquerdo do domínio, selecione **as ligações hostname** . As ligações de nome anfitrião de todos os serviços da Azure estão listadas aqui.

![Screenshot que mostra a página de encadernações do nome anfitrião.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Não é possível eliminar o Domínio do Serviço de Aplicações até que todas as ligações do nome anfitrião sejam eliminadas.

Elimine cada ligação de nome de anfitrião selecionando **...**  >  **Eliminar** . Depois de todas as ligações serem eliminadas, **selecione Save** .

![Screenshot que mostra onde apagar as ligações do nome anfitrião.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Cancelar ou apagar

No menu esquerdo do domínio, selecione **Overview** . 

Se o período de cancelamento do domínio adquirido não tiver decorrido, selecione **Cancelar a compra** . Caso contrário, vê-se um botão **Eliminar.** Para eliminar o domínio sem reembolso, selecione **Delete** .

![Screenshot que mostra onde apagar ou cancelar um domínio adquirido.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Para confirmar a operação, selecione **Sim** .

Após a operação estar concluída, o domínio é libertado da sua subscrição e disponível para qualquer pessoa comprar novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar o URL predefinido para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Para direcioná-los para uma subdirectória, por `public` exemplo, consulte [URL padrão direto para um diretório personalizado](app-service-web-tutorial-custom-domain.md#virtualdir).

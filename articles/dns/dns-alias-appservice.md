---
title: Aplicativos web Azure equilibrados em carga no ápice da zona
description: Use um registo de pseudónimo sintetizador de DNS Azure para hospedar aplicações web equilibradas em carga no ápice da zona
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937374"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Aplicativos web Azure equilibrados em carga no ápice da zona

O protocolo DNS impede a atribuição de qualquer outra coisa que não um registo A ou AAAA no ápice da zona. Um ápice de zona de exemplo é contoso.com. Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás do Traffic Manager. Não é possível apontar para o perfil do Gestor de Tráfego a partir do registo de ápice da zona. Como resultado, os proprietários de aplicações devem utilizar uma suver. Um redirecionamento para a camada de aplicação deve redirecionar do ápice da zona para outro domínio. Um exemplo é um redirecionamento de contoso.com para www\.contoso.com. Este arranjo apresenta um único ponto de falha para a função de redirecionamento.

Com registos de pseudónimos, este problema já não existe. Agora, os proprietários de aplicações podem indicar o seu registo de ápice de zona para um perfil do Gestor de Tráfego que tem pontos finais externos. Os proprietários de aplicações podem apontar para o mesmo perfil do Traffic Manager que é usado para qualquer outro domínio dentro da sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com podem apontar para o mesmo perfil do Gestor de Tráfego. É o caso, desde que o perfil do Gestor de Tráfego tenha apenas pontos finais externos configurados.

Neste artigo, aprende a criar um registo de pseudónimos para o seu ápice de domínio e configurar os pontos finais do seu perfil do Gestor de Tráfego para as suas aplicações web.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter um nome de domínio disponível que possa alojar no DNS do Azure para testar. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

Para obter instruções sobre como alojar o seu domínio no DNS do Azure, veja [Tutorial: Alojar o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo utilizado para este tutorial é o contoso.com, mas utilize o seu próprio nome de domínio.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para manter todos os recursos utilizados neste artigo.

## <a name="create-app-service-plans"></a>Criar planos de serviço de aplicações

Crie dois planos de Serviço de Aplicações Web no seu grupo de recursos utilizando a tabela seguinte para informações de configuração. Para obter mais informações sobre a criação de um plano de Serviço de Aplicações, consulte Gerir um plano de Serviço de [Aplicações em Azure](../app-service/app-service-plan-manage.md).


|Nome  |Sistema Operativo  |Localização  |Escalão de Preço  |
|---------|---------|---------|---------|
|ASP-01     |Windows|E.U.A. Leste|Dev/Teste D1-Shared|
|ASP-02     |Windows|E.U.A. Central|Dev/Teste D1-Shared|

## <a name="create-app-services"></a>Criar serviços de aplicações

Crie duas aplicações web, uma em cada plano de Serviço de Aplicações.

1. No canto superior esquerdo da página do portal Azure, selecione **Criar um recurso**.
2. Digite **a aplicação Web** na barra de pesquisa e prima Enter.
3. Selecione **Web App**.
4. Selecione **Criar**.
5. Aceite os predefinidos e utilize a seguinte tabela para configurar as duas aplicações web:

   |Nome<br>(deve ser único dentro de .azurewebsites.net)|Grupo de Recursos |Pilha de tempo de execução|Região|Plano de Serviço de Aplicações/Localização
   |---------|---------|-|-|-------|
   |App-01|Usar existente<br>Selecione o seu grupo de recursos|.NET Core 2.2|E.U.A. Leste|ASP-01(D1)|
   |App-02|Usar existente<br>Selecione o seu grupo de recursos|.NET Core 2.2|E.U.A. Central|ASP-02(D1)|

### <a name="gather-some-details"></a>Reúna alguns detalhes

Agora precisa de notar o endereço IP e o nome do anfitrião para as aplicações web.

1. Abra o seu grupo de recursos e selecione a sua primeira aplicação web **(App-01** neste exemplo).
2. Na coluna esquerda, selecione **Propriedades**.
3. Note o endereço em **URL**, e em **endereços IP outbound** note o primeiro endereço IP na lista. Utilizará esta informação mais tarde quando configurar os pontos finais do Gestor de Tráfego.
4. Repita para **app-02**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego no seu grupo de recursos. Utilize as predefinições e escreva um nome único no espaço de nome trafficmanager.net.

Para obter informações sobre a criação de um perfil de Gestor de Tráfego, consulte [Quickstart: Criar um perfil de Gestor de Tráfego para uma aplicação web altamente disponível.](../traffic-manager/quickstart-create-traffic-manager-profile.md)

### <a name="create-endpoints"></a>Criar pontos finais

Agora pode criar os pontos finais para as duas aplicações web.

1. Abra o seu grupo de recursos e selecione o seu perfil de Gestor de Tráfego.
2. Na coluna esquerda, selecione **Pontos Finais**.
3. Selecione **Adicionar**.
4. Utilize o quadro seguinte para configurar os pontos finais:

   |Tipo  |Nome  |Visar  |Localização  |Definições personalizadas do cabeçalho|
   |---------|---------|---------|---------|---------|
   |Ponto final externo     |Fim-01|Endereço IP que gravou para App-01|E.U.A. Leste|anfitrião:\<o URL que gravou para o App-01\><br>Exemplo: **host:app-01.azurewebsites.net**|
   |Ponto final externo     |Fim-02|Endereço IP que gravou para App-02|E.U.A. Central|anfitrião:\<o URL que gravou para app-02\><br>Exemplo: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Criar zona DNS

Pode utilizar uma zona DNS existente para testar, ou pode criar uma nova zona. Para criar e delegar uma nova zona DNS em Azure, consulte [Tutorial: Hospedar o seu domínio em DNS Azure](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Adicione um registo TXT para validação de domínio personalizado

Quando adicionar um nome de anfitrião personalizado às suas aplicações web, procurará um registo Específico de TXT para validar o seu domínio.

1. Abra o seu grupo de recursos e selecione a zona DNS.
2. Selecione **Conjunto de registos**.
3. Adicione o conjunto de registos utilizando a tabela seguinte. Para o valor, utilize o URL da aplicação web real que gravou anteriormente:

   |Nome  |Tipo  |Valor|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Adicione um domínio personalizado para ambas as aplicações web.

1. Abra o seu grupo de recursos e selecione a sua primeira aplicação web.
2. Na coluna esquerda, selecione **domínios personalizados**.
3. Em **domínios personalizados,** selecione **Adicionar domínio personalizado**.
4. Sob **domínio personalizado,** escreva o seu nome de domínio personalizado. Por exemplo, contoso.com.
5. Selecione **Validar**.

   O seu domínio deve passar na validação e mostrar marcas de verificação verdes ao lado da disponibilidade do **Hostname** e **da propriedade do Domínio**.
5. Selecione **Adicionar domínio personalizado**.
6. Para ver o novo nome de anfitrião sob **nomes hostis atribuídos ao site,** atualize o seu navegador. A atualização na página nem sempre mostra mudanças de imediato.
7. Repita este procedimento para a sua segunda aplicação web.

## <a name="add-the-alias-record-set"></a>Adicione o conjunto de recordes de pseudónimos

Agora adicione um recorde de pseudónimo para o ápice da zona.

1. Abra o seu grupo de recursos e selecione a zona DNS.
2. Selecione **Conjunto de registos**.
3. Adicione o conjunto de registos utilizando a tabela seguinte:

   |Nome  |Tipo  |Conjunto de recordes de alias  |Tipo de pseudónimo  |Recurso do Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Sim|Recurso do Azure|Gestor de Tráfego - o seu perfil|


## <a name="test-your-web-apps"></a>Teste as suas aplicações web

Agora pode testar para se certificar de que pode chegar à sua aplicação web e que está a ser equilibrada.

1. Abra um navegador web e navegue para o seu domínio. Por exemplo, contoso.com. Deve ver a página de aplicação predefinida.
2. Pare a sua primeira aplicação web.
3. Feche o seu navegador web e espere alguns minutos.
4. Inicie o seu navegador web e navegue para o seu domínio. Ainda deve ver a página de aplicações predefinidas na Web.
5. Pare a sua segunda aplicação web.
6. Feche o seu navegador web e espere alguns minutos.
7. Inicie o seu navegador web e navegue para o seu domínio. Deve ver o Error 403, indicando que a aplicação web está parada.
8. Inicie a sua segunda aplicação web.
9. Feche o seu navegador web e espere alguns minutos.
10. Inicie o seu navegador web e navegue para o seu domínio. Deve ver novamente a página de aplicações predefinidas na Web.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registos de pseudónimos, consulte os seguintes artigos:

- [Tutorial: Configure um registo de pseudónimo para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: Configure um registo de pseudónimo para apoiar nomes de domínio apex com Traffic Manager](tutorial-alias-tm.md)
- [FAQ sobre DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Para aprender a migrar um nome DNS ativo, consulte [Migrate um nome DNS ativo para o Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

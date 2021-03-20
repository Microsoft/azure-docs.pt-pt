---
title: Hospedar aplicativos web Azure equilibrados na zona apex
description: Use um registo de pseudónimo Azure DNS para hospedar aplicações web equilibradas na zona
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 72adb2732eb0832589cbc25fb7e4288eb1899214
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954516"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hospedar aplicativos web Azure equilibrados na zona apex

O protocolo DNS impede a atribuição de qualquer outra coisa que não um registo A ou AAAA no ápice da zona. Um exemplo de zona ápice é contoso.com. Esta restrição apresenta um problema para os proprietários de aplicações que têm aplicações equilibradas por trás do Gestor de Tráfego. Não é possível apontar para o perfil do Gestor de Tráfego a partir do registo do ápice da zona. Como resultado, os proprietários de aplicações devem utilizar uma solução alternativa. Um redirecionamento na camada de aplicação deve ser redirecionado do ápice da zona para outro domínio. Um exemplo é um redirecionamento de contoso.com para www \. contoso.com. Este arranjo apresenta um único ponto de falha para a função de redirecionamento.

Com registos pseudónimos, este problema já não existe. Agora os proprietários de aplicações podem apontar o seu registo de zona para um perfil de Gestor de Tráfego que tenha pontos finais externos. Os proprietários de aplicações podem apontar para o mesmo perfil de Gestor de Tráfego que é usado para qualquer outro domínio dentro da sua zona de DNS.

Por exemplo, contoso.com e www \. contoso.com podem apontar para o mesmo perfil de Gestor de Tráfego. É o caso, desde que o perfil do Gestor de Tráfego tenha apenas pontos finais externos configurados.

Neste artigo, aprende-se a criar um registo de pseudónimos para o seu ápice de domínio e configura os pontos finais do seu Gestor de Tráfego para as suas aplicações web.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter um nome de domínio disponível que possa alojar no DNS do Azure para testar. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

Para obter instruções sobre como alojar o seu domínio no DNS do Azure, veja [Tutorial: Alojar o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo utilizado para este tutorial é o contoso.com, mas utilize o seu próprio nome de domínio.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para deter todos os recursos utilizados neste artigo.

## <a name="create-app-service-plans"></a>Criar planos de Serviço de Aplicações

Crie dois planos de Serviço de Aplicações Web no seu grupo de recursos utilizando a seguinte tabela para obter informações de configuração. Para obter mais informações sobre a criação de um plano de Serviço de Aplicações, consulte [Gerir um plano de Serviço de Aplicações em Azure.](../app-service/app-service-plan-manage.md)


|Name  |Sistema Operativo  |Localização  |Escalão de Preço  |
|---------|---------|---------|---------|
|ASP-01     |Windows|E.U.A. Leste|Dev/Test D1-Shared|
|ASP-02     |Windows|E.U.A. Central|Dev/Test D1-Shared|

## <a name="create-app-services"></a>Criar Serviços de Aplicações

Crie duas aplicações web, uma em cada plano de Serviço de Aplicações.

1. No canto superior esquerdo da página do portal Azure, selecione **Criar um recurso**.
2. Digite **aplicativo Web** na barra de pesquisa e prima Enter.
3. Selecione **Web App**.
4. Selecione **Criar**.
5. Aceite os predefinidos e use a seguinte tabela para configurar as duas aplicações web:

   |Name<br>(deve ser único dentro de .azurewebsites.net)|Grupo de Recursos |Pilha de runtime|Region|Plano de Serviço de Aplicações/Localização
   |---------|---------|-|-|-------|
   |App-01|Utilizar existente<br>Selecione o seu grupo de recursos|.NET Core 2.2|E.U.A. Leste|ASP-01(D1)|
   |App-02|Utilizar existente<br>Selecione o seu grupo de recursos|.NET Core 2.2|E.U.A. Central|ASP-02(D1)|

### <a name="gather-some-details"></a>Recolha alguns detalhes

Agora você precisa notar o endereço IP e o nome de anfitrião para as aplicações web.

1. Abra o seu grupo de recursos e selecione a sua primeira aplicação web **(App-01** neste exemplo).
2. Na coluna esquerda, selecione **Propriedades**.
3. Note o endereço em **URL**, e em **endereços IP outbound** note o primeiro endereço IP na lista. Usará esta informação mais tarde quando configurar os pontos finais do seu Gestor de Tráfego.
4. Repita para **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego no seu grupo de recursos. Use as predefinições e escreva um nome único dentro do trafficmanager.net espaço de nome.

Para obter informações sobre a criação de um perfil de Gestor de Tráfego, consulte [Quickstart: Crie um perfil de Gestor de Tráfego para uma aplicação web altamente disponível.](../traffic-manager/quickstart-create-traffic-manager-profile.md)

### <a name="create-endpoints"></a>Criar pontos finais

Agora pode criar os pontos finais para as duas aplicações web.

1. Abra o seu grupo de recursos e selecione o seu perfil de Gestor de Tráfego.
2. Na coluna esquerda, selecione **Endpoints**.
3. Selecione **Adicionar**.
4. Utilize a seguinte tabela para configurar os pontos finais:

   |Tipo  |Name  |Destino  |Localização  |Configurações personalizadas do cabeçalho|
   |---------|---------|---------|---------|---------|
   |Ponto final externo     |Fim de 01|Endereço IP que gravou para App-01|E.U.A. Leste|anfitrião:\<the URL you recorded for App-01\><br>Exemplo: **host:app-01.azurewebsites.net**|
   |Ponto final externo     |Fim de 02|Endereço IP que gravou para App-02|E.U.A. Central|anfitrião:\<the URL you recorded for App-02\><br>Exemplo: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Criar zona DNS

Pode utilizar uma zona DE DNS existente para testes, ou pode criar uma nova zona. Para criar e delegar uma nova zona DNS em Azure, consulte [Tutorial: Hospedar o seu domínio em Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Adicione um registo TXT para validação de domínio personalizado

Quando adicionar um nome de anfitrião personalizado às suas aplicações web, procurará um registo TXT específico para validar o seu domínio.

1. Abra o seu grupo de recursos e selecione a zona DNS.
2. Selecione **Conjunto de registos**.
3. Adicione o conjunto de recordes utilizando a tabela seguinte. Para obter o valor, utilize o URL da aplicação web que gravou anteriormente:

   |Nome  |Tipo  |Valor|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Adicione um domínio personalizado para ambas as aplicações web.

1. Abra o seu grupo de recursos e selecione a sua primeira aplicação web.
2. Na coluna esquerda, selecione **domínios personalizados**.
3. Em **Domínios Personalizados**, selecione **Adicionar domínio personalizado.**
4. Em **domínio personalizado,** escreva o seu nome de domínio personalizado. Por exemplo, contoso.com.
5. Selecione **Validar**.

   O seu domínio deve passar a validação e mostrar marcas de verificação verdes ao lado **da disponibilidade do Hostname** e **da propriedade do Domínio**.
5. **Selecione Adicionar domínio personalizado**.
6. Para ver o novo nome de anfitrião em **Hostnames atribuído ao site,** refresque o seu navegador. A atualização na página nem sempre mostra mudanças imediatamente.
7. Repita este procedimento para a sua segunda aplicação web.

## <a name="add-the-alias-record-set"></a>Adicione o conjunto de recordes de pseudónimos

Agora adicione um recorde de pseudónimo para o ápice da zona.

1. Abra o seu grupo de recursos e selecione a zona DNS.
2. Selecione **Conjunto de registos**.
3. Adicione o conjunto de recordes utilizando a seguinte tabela:

   |Nome  |Tipo  |Conjunto de registos de alias  |Tipo de alias  |Recurso do Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Yes|Recurso do Azure|Gestor de Tráfego - o seu perfil|


## <a name="test-your-web-apps"></a>Teste as suas aplicações web

Agora pode testar para se certificar de que pode chegar à sua aplicação web e que está a ser carregada equilibrada.

1. Abra um navegador web e navegue no seu domínio. Por exemplo, contoso.com. Você deve ver a página de aplicativos web padrão.
2. Pare a sua primeira aplicação web.
3. Feche o seu navegador web e espere alguns minutos.
4. Inicie o seu navegador web e navegue para o seu domínio. Você ainda deve ver a página de aplicações web padrão.
5. Pare a sua segunda aplicação web.
6. Feche o seu navegador web e espere alguns minutos.
7. Inicie o seu navegador web e navegue para o seu domínio. Deve ver o Erro 403, indicando que a aplicação web está parada.
8. Inicie a sua segunda aplicação web.
9. Feche o seu navegador web e espere alguns minutos.
10. Inicie o seu navegador web e navegue para o seu domínio. Deve voltar a ver a página de aplicações web padrão.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registos de pseudónimos, consulte os seguintes artigos:

- [Tutorial: Configurar um registo de pseudónimo para se referir a um endereço IP público azul](tutorial-alias-pip.md)
- [Tutorial: Configurar um registo de alias para suportar os nomes de domínio apex com o Gestor de Tráfego](tutorial-alias-tm.md)
- [FAQ sobre DNS](./dns-faq.md#alias-records)

Para aprender a migrar um nome DNS ativo, consulte [Migrar um nome DNS ativo para o Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
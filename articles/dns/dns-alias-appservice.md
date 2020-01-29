---
title: Hospedar aplicativos Web do Azure com balanceamento de carga no Apex da zona
description: Usar um registro de alias do DNS do Azure para hospedar aplicativos Web com balanceamento de carga no Apex da zona
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: allensu
ms.openlocfilehash: a673a74f8f6f919e7ebb7fc3b065ee0742ab3a10
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212372"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hospedar aplicativos Web do Azure com balanceamento de carga no Apex da zona

O protocolo DNS impede a atribuição de algo diferente de um registro A ou AAAA no Apex da zona. Um exemplo de Apex de zona é contoso.com. Essa restrição apresenta um problema para proprietários de aplicativos que têm aplicativos com balanceamento de carga por trás do Gerenciador de tráfego. Não é possível apontar para o perfil do Gerenciador de tráfego do registro de Apex da zona. Como resultado, os proprietários do aplicativo devem usar uma solução alternativa. Um redirecionamento na camada de aplicativo deve ser redirecionado do Apex da zona para outro domínio. Um exemplo é um redirecionamento de contoso.com para www\.contoso.com. Essa organização apresenta um ponto único de falha para a função de redirecionamento.

Com os registros de alias, esse problema não existe mais. Agora, os proprietários do aplicativo podem apontar seu registro de Apex de zona para um perfil do Gerenciador de tráfego que tem pontos de extremidade externos. Os proprietários do aplicativo podem apontar para o mesmo perfil do Gerenciador de tráfego usado para qualquer outro domínio dentro de sua zona DNS.

Por exemplo, contoso.com e www\.contoso.com podem apontar para o mesmo perfil do Gerenciador de tráfego. Esse é o caso, desde que o perfil do Gerenciador de tráfego tenha apenas pontos de extremidade externos configurados.

Neste artigo, você aprenderá a criar um registro de alias para o seu domínio Apex e a configurar seus pontos de extremidade de perfil do Traffic Manager para seus aplicativos Web.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter um nome de domínio disponível que possa alojar no DNS do Azure para testar. Deve ter controlo total sobre este domínio. O controlo total inclui a capacidade de definir os registos do servidor de nomes (NS) do domínio.

Para obter instruções sobre como alojar o seu domínio no DNS do Azure, veja [Tutorial: Alojar o seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

O domínio de exemplo utilizado para este tutorial é o contoso.com, mas utilize o seu próprio nome de domínio.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos para manter todos os recursos usados neste artigo.

## <a name="create-app-service-plans"></a>Criar planos do serviço de aplicativo

Crie dois planos do serviço de aplicativo Web em seu grupo de recursos usando a tabela a seguir para obter informações de configuração. Para obter mais informações sobre como criar um plano do serviço de aplicativo, consulte [gerenciar um plano do serviço de aplicativo no Azure](../app-service/app-service-plan-manage.md).


|Nome  |Sistema Operativo  |Localização  |Escalão de Preço  |
|---------|---------|---------|---------|
|ASP-01     |Windows|E.U.A. Leste|Desenvolvimento/teste D1-compartilhado|
|ASP-02     |Windows|E.U.A. Central|Desenvolvimento/teste D1-compartilhado|

## <a name="create-app-services"></a>Criar serviços de aplicativos

Crie dois aplicativos Web, um em cada plano do serviço de aplicativo.

1. No canto superior esquerdo da página portal do Azure, selecione **criar um recurso**.
2. Digite **aplicativo Web** na barra de pesquisa e pressione Enter.
3. Selecione **aplicativo Web**.
4. Selecione **Criar**.
5. Aceite os padrões e use a tabela a seguir para configurar os dois aplicativos Web:

   |Nome<br>(deve ser exclusivo em. azurewebsites.net)|Grupo de Recursos |Pilha de tempo de execução|Região|Local/plano do serviço de aplicativo
   |---------|---------|-|-|-------|
   |App-01|Usar existente<br>Selecione seu grupo de recursos|.NET Core 2.2|E.U.A. Leste|ASP-01 (D1)|
   |Aplicativo-02|Usar existente<br>Selecione seu grupo de recursos|.NET Core 2.2|E.U.A. Central|ASP-02 (D1)|

### <a name="gather-some-details"></a>Reunir alguns detalhes

Agora, você precisa anotar o endereço IP e o nome do host para os aplicativos Web.

1. Abra seu grupo de recursos e selecione seu primeiro aplicativo Web (**app-01** neste exemplo).
2. Na coluna à esquerda, selecione **Propriedades**.
3. Anote o endereço em **URL**e, em **endereços IP de saída** , anote o primeiro endereço IP na lista. Você usará essas informações posteriormente ao configurar os pontos de extremidade do Gerenciador de tráfego.
4. Repita para o **app-02**.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil do Gerenciador de tráfego em seu grupo de recursos. Use os padrões e digite um nome exclusivo dentro do namespace trafficmanager.net.

Para obter informações sobre como criar um perfil do Gerenciador de tráfego, consulte [início rápido: criar um perfil do Gerenciador de tráfego para um aplicativo Web altamente disponível](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Criar pontos finais

Agora você pode criar os pontos de extremidade para os dois aplicativos Web.

1. Abra seu grupo de recursos e selecione seu perfil do Gerenciador de tráfego.
2. Na coluna à esquerda, selecione **pontos de extremidade**.
3. Selecione **Adicionar**.
4. Use a tabela a seguir para configurar os pontos de extremidade:

   |Tipo  |Nome  |Destino  |Localização  |Configurações de cabeçalho personalizadas|
   |---------|---------|---------|---------|---------|
   |Ponto de extremidade externo     |Fim-01|Endereço IP que você registrou para o app-01|E.U.A. Leste|host:\<a URL que você registrou para o app-01\><br>Exemplo: **host: app-01.azurewebsites.net**|
   |Ponto de extremidade externo     |Fim de 02|Endereço IP que você registrou para o aplicativo-02|E.U.A. Central|host:\<a URL que você registrou para o app-02\><br>Exemplo: **host: app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Criar zona DNS

Você pode usar uma zona DNS existente para teste ou pode criar uma nova zona. Para criar e delegar uma nova zona DNS no Azure, consulte [tutorial: hospedar seu domínio no DNS do Azure](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Adicionar um registro TXT para validação de domínio personalizada

Quando você adiciona um nome de host personalizado aos seus aplicativos Web, ele procurará um registro TXT específico para validar seu domínio.

1. Abra o grupo de recursos e selecione a zona DNS.
2. Selecione **Conjunto de registos**.
3. Adicione o conjunto de registros usando a tabela a seguir. Para o valor, use a URL do aplicativo Web real que você gravou anteriormente:

   |Nome  |Tipo  |Valor|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Adicione um domínio personalizado para ambos os aplicativos Web.

1. Abra seu grupo de recursos e selecione seu primeiro aplicativo Web.
2. Na coluna à esquerda, selecione **domínios personalizados**.
3. Em **domínios personalizados**, selecione **Adicionar domínio personalizado**.
4. Em **domínio personalizado**, digite seu nome de domínio personalizado. Por exemplo, contoso.com.
5. Selecione **Validar**.

   Seu domínio deve passar na validação e Mostrar marcas de seleção verdes ao lado de **disponibilidade do nome do host** e **Propriedade do domínio**.
5. Selecione **Adicionar domínio personalizado**.
6. Para ver o novo nome do host em **nomes de host atribuídos ao site**, atualize seu navegador. A atualização na página nem sempre mostra alterações imediatamente.
7. Repita este procedimento para seu segundo aplicativo Web.

## <a name="add-the-alias-record-set"></a>Adicionar o conjunto de registros de alias

Agora, adicione um registro de alias para o Apex da zona.

1. Abra o grupo de recursos e selecione a zona DNS.
2. Selecione **Conjunto de registos**.
3. Adicione o conjunto de registros usando a tabela a seguir:

   |Nome  |Tipo  |Conjunto de registros de alias  |Tipo de alias  |Recurso do Azure|
   |---------|---------|---------|---------|-----|
   |@     |A|Sim|Recurso do Azure|Gerenciador de tráfego-seu perfil|


## <a name="test-your-web-apps"></a>Testar seus aplicativos Web

Agora você pode testar para ter certeza de que pode acessar seu aplicativo Web e que está sendo balanceado por carga.

1. Abra um navegador da Web e navegue até seu domínio. Por exemplo, contoso.com. Você deverá ver a página padrão do aplicativo Web.
2. Pare seu primeiro aplicativo Web.
3. Feche o navegador da Web e aguarde alguns minutos.
4. Inicie o navegador da Web e navegue até o seu domínio. Você ainda deve ver a página padrão do aplicativo Web.
5. Pare seu segundo aplicativo Web.
6. Feche o navegador da Web e aguarde alguns minutos.
7. Inicie o navegador da Web e navegue até o seu domínio. Você deverá ver o erro 403, indicando que o aplicativo Web foi interrompido.
8. Inicie seu segundo aplicativo Web.
9. Feche o navegador da Web e aguarde alguns minutos.
10. Inicie o navegador da Web e navegue até o seu domínio. Você deve ver a página padrão do aplicativo Web novamente.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os registros de alias, consulte os seguintes artigos:

- [Tutorial: configurar um registro de alias para se referir a um endereço IP público do Azure](tutorial-alias-pip.md)
- [Tutorial: configurar um registro de alias para dar suporte a nomes de domínio Apex com o Gerenciador de tráfego](tutorial-alias-tm.md)
- [FAQ sobre DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Para saber como migrar um nome DNS ativo, consulte [migrar um nome DNS ativo para Azure app serviço](../app-service/manage-custom-dns-migrate-domain.md).

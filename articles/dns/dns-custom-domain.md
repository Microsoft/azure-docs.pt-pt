---
title: Integrar o DNS do Azure com os seus recursos do Azure
description: Saiba como utilizar o DNS do Azure ao longo para fornecer o DNS para os seus recursos do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/18/2019
ms.author: victorh
ms.openlocfilehash: e8163ad34046261d9671c57db2cd2d2052fe35e0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550792"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Utilizar o DNS do Azure para fornecer definições de domínio personalizado para um serviço do Azure

O DNS do Azure fornece DNS para um domínio personalizado para qualquer um dos seus recursos do Azure que o suporte de domínios personalizados ou que têm um nome de domínio completamente qualificado (FQDN). Um exemplo é tiver uma aplicação web do Azure e pretende que os utilizadores para acessá-lo, seja usando o contoso.com, ou www.contoso.com como um FQDN. Este artigo orienta-o por meio de configurar o serviço do Azure com o DNS do Azure para a utilização de domínios personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o DNS do Azure para o seu domínio personalizado, primeiro tem de delegar o seu domínio ao DNS do Azure. Visite [delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores de nome para a delegação. Assim que o seu domínio é delegado à sua zona DNS do Azure, é possível configurar os registros DNS necessários.

Pode configurar um personalizado ou o domínio personalizado para [aplicações de funções do Azure](#azure-function-app), [endereços IP públicos](#public-ip-address), [serviço de aplicações (aplicações Web)](#app-service-web-apps), [doarmazenamentodeBLOBs](#blob-storage), e [CDN do Azure](#azure-cdn).

## <a name="azure-function-app"></a>Aplicação de funções do Azure

Para configurar um domínio personalizado para aplicações de função do Azure, um registo CNAME é criado, bem como a configuração da aplicação de função em si.
 
Navegue para **aplicação de funções** e selecione a aplicação de função. Clique em **funcionalidades de plataforma** e, em **redes** clique **domínios personalizados**.

![Painel de aplicação de função](./media/dns-custom-domain/functionapp.png)

Tenha em atenção o atual url no **domínios personalizados** painel, este endereço é utilizado como o alias para o registo DNS criado.

![Painel de domínio personalizado](./media/dns-custom-domain/functionshostname.png)

Navegue para a zona DNS e clique em **+ conjunto de registos**. Preencha as seguintes informações sobre o **adicionar conjunto de registos** painel e clique em **OK** para criá-lo.

|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Name     | myfunctionapp        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Utilize um registo CNAME está a utilizar um alias.        |
|TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade de TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | adatumfunction.azurewebsites.net        | O nome DNS está a criar o alias, neste exemplo é o nome DNS adatumfunction.azurewebsites.net fornecido por predefinição para a aplicação de funções.        |

Navegue de volta para a sua aplicação function app, clique em **funcionalidades de plataforma**e, em **redes** clique em **domínios personalizados**, em seguida em **nomes de anfitrião personalizado** clique em **+ Adicionar nome de anfitrião**.

Na **Adicionar nome de anfitrião** painel, introduza o registo CNAME no **nome do anfitrião** campo de texto e clique em **validar**. Se o registo for encontrado, o **Adicionar nome de anfitrião** botão aparece. Clique em **Adicionar nome de anfitrião** para adicionar o alias.

![Painel de nome de anfitrião de adicionar aplicações de funções](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Endereço IP público

Para configurar um domínio personalizado para recursos, como o Gateway de aplicação, o Balanceador de carga, o serviço em nuvem, o VMs do Resource Manager de endereços de serviços que utilizam um IP público, e as VMs clássicas, um registo é usada.

Navegue para **Networking** > **endereço IP público**, selecione o recurso de IP público e clique em **configuração**. Notate o endereço IP mostrado.

![painel do ip público](./media/dns-custom-domain/publicip.png)

Navegue para a zona DNS e clique em **+ conjunto de registos**. Preencha as seguintes informações sobre o **adicionar conjunto de registos** painel e clique em **OK** para criá-lo.


|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Name     | mywebserver        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | A        | Utilize um registo, como o recurso é um endereço IP.        |
|TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade de TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Endereço IP     | <your ip address>       | O endereço IP público.|

![criar um registo](./media/dns-custom-domain/arecord.png)

Depois de criar o registo, execute `nslookup` para validar o registo resolve o objetivo.

![pesquisa de dns de ip público](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Serviço de aplicações (aplicações Web)

As seguintes etapas guiarão por meio de configurar um domínio personalizado para uma aplicação de web do serviço de aplicações.

Navegue para **serviço de aplicações** e selecione o recurso que está a configurar um nome de domínio personalizado e clique em **domínios personalizados**.

Tenha em atenção o atual url no **domínios personalizados** painel, este endereço é utilizado como o alias para o registo DNS criado.

![Painel de domínios personalizados](./media/dns-custom-domain/url.png)

Navegue para a zona DNS e clique em **+ conjunto de registos**. Preencha as seguintes informações sobre o **adicionar conjunto de registos** painel e clique em **OK** para criá-lo.


|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Name     | mywebserver        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Utilize um registo CNAME está a utilizar um alias. Se o recurso utilizado um endereço IP, seria usado um registo.        |
|TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade de TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | webserver.azurewebsites.net        | O nome DNS está a criar o alias, neste exemplo é o nome DNS webserver.azurewebsites.net fornecido por predefinição para a aplicação web.        |


![Criar um registo CNAME](./media/dns-custom-domain/createcnamerecord.png)

Navegue de volta para o serviço de aplicações que está configurado para o nome de domínio personalizado. Clique em **domínios personalizados**, em seguida, clique em **os nomes de anfitrião**. Para adicionar o registo CNAME que criou, clique em **+ Adicionar nome de anfitrião**.

![figura 1](./media/dns-custom-domain/figure1.png)

Depois do processo estiver concluído, execute **nslookup** validar a resolução de nomes está a funcionar.

![figura 1](./media/dns-custom-domain/finalnslookup.png)

Para saber mais sobre o mapeamento de um domínio personalizado no serviço de aplicações, visite [mapear um nome DNS existente personalizado para aplicações Web do Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Se precisar de comprar um domínio personalizado, visite [comprar um nome de domínio personalizado para aplicações Web do Azure](../app-service/manage-custom-dns-buy-domain.md) para saber mais sobre os domínios do serviço de aplicações.

## <a name="blob-storage"></a>Armazenamento de blobs

As seguintes etapas guiarão por meio de um registo CNAME para uma conta de armazenamento de Blobs com o método asverifiy a configurar. Esse método garante que não existe nenhum tempo de inatividade.

Navegue para **armazenamento** > **contas de armazenamento**, selecione a sua conta de armazenamento e clique em **Custom domain**. Notate o FQDN no passo 2, este valor é utilizado para criar o primeiro registo CNAME

![domínio personalizado de armazenamento de BLOBs](./media/dns-custom-domain/blobcustomdomain.png)

Navegue para a zona DNS e clique em **+ conjunto de registos**. Preencha as seguintes informações sobre o **adicionar conjunto de registos** painel e clique em **OK** para criá-lo.


|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Name     | asverify.mystorageaccount        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Utilize um registo CNAME está a utilizar um alias.        |
|TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade de TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | O nome DNS está a criar o alias, neste exemplo é o nome DNS asverify.adatumfunctiona9ed.blob.core.windows.net fornecido por predefinição para a conta de armazenamento.        |

Navegue de volta para a sua conta de armazenamento ao clicar em **armazenamento** > **contas de armazenamento**, selecione a sua conta de armazenamento e clique em **Custom domain**. Escreva o alias que criou sem o prefixo de asverifiy na caixa de texto, verificação * * Utilizar validação de CNAME indireta e clique em **guardar**. Uma vez concluído este passo, regresse à sua zona DNS e crie um registo CNAME sem o prefixo de asverifiy.  Após esse ponto, é seguro eliminar o registo CNAME com o prefixo de cdnverify.

![domínio personalizado de armazenamento de BLOBs](./media/dns-custom-domain/indirectvalidate.png)

Validar a resolução de DNS ao executar `nslookup`

Para saber mais sobre o mapeamento de um domínio personalizado para um ponto de extremidade de armazenamento de BLOBs, visite [configurar um nome de domínio personalizado para o ponto final de armazenamento de BLOBs](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>CDN do Azure

As seguintes etapas guiarão por meio de configurar um registo CNAME para um ponto final da CDN através do método cdnverify. Esse método garante que não existe nenhum tempo de inatividade.

Navegue para **Networking** > **perfis da CDN**, selecione o perfil de CDN.

Selecione o ponto final que está a trabalhar e clique em **+ domínio personalizado**. Tenha em atenção a **nome de anfitrião do ponto de extremidade** como este valor é o registo que o registo CNAME aponta para.

![Domínio personalizado de CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navegue para a zona DNS e clique em **+ conjunto de registos**. Preencha as seguintes informações sobre o **adicionar conjunto de registos** painel e clique em **OK** para criá-lo.

|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Name     | cdnverify.mycdnendpoint        | Este valor, juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Utilize um registo CNAME está a utilizar um alias.        |
|TTL     | 1        | 1 é utilizado para 1 hora        |
|Unidade de TTL     | Horas        | Horas são utilizadas como a medição do tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | O nome DNS está a criar o alias, neste exemplo é o nome DNS cdnverify.adatumcdnendpoint.azureedge.net fornecido por predefinição para a conta de armazenamento.        |

Navegue de volta para o ponto final de CDN ao clicar em **Networking** > **perfis da CDN**e selecione o perfil de CDN. Clique em **+ domínio personalizado** e introduza o seu alias de registo CNAME sem o prefixo de cdnverify e clique em **Add**.

Uma vez concluído este passo, regresse à sua zona DNS e crie um registo CNAME sem o prefixo de cdnverify.  Após esse ponto, é seguro eliminar o registo CNAME com o prefixo de cdnverify. Para obter mais informações sobre a CDN e como configurar um domínio personalizado sem o passo de registo intermediários visite [conteúdo de mapa do Azure CDN para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes

Saiba como [configurar DNS inverso para os serviços alojados no Azure](dns-reverse-dns-for-azure-services.md).

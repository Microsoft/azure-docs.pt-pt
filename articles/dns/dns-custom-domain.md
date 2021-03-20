---
title: Integre o Azure DNS com os seus recursos Azure - Azure DNS
description: Neste artigo, aprenda a usar o Azure DNS para fornecer DNS para os seus recursos Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92487403"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Utilize o Azure DNS para fornecer definições de domínio personalizadas para um serviço Azure

O Azure DNS fornece DNS para um domínio personalizado para qualquer um dos seus recursos Azure que suportam domínios personalizados ou que tenham um nome de domínio totalmente qualificado (FQDN). Um exemplo é que tem uma aplicação web Azure e quer que os seus utilizadores acedam a ela utilizando contoso.com, ou www \. contoso.com como FQDN. Este artigo acompanha-o através da configuração do seu serviço Azure com o Azure DNS para a utilização de domínios personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o Azure DNS para o seu domínio personalizado, tem primeiro de delegar o seu domínio no Azure DNS. Visite [delegado de um domínio ao Azure DNS](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores do seu nome para a delegação. Uma vez que o seu domínio é delegado à sua zona Azure DNS, é capaz de configurar os registos DNS necessários.

Pode configurar uma vaidade ou domínio personalizado para [apps de função Azure,](#azure-function-app) [endereços IP públicos,](#public-ip-address) [Serviço de Aplicações (Web Apps)](#app-service-web-apps), [armazenamento blob](#blob-storage)e [CDN Azure](#azure-cdn).

## <a name="azure-function-app"></a>App de função Azure

Para configurar um domínio personalizado para aplicações de função Azure, é criado um registo CNAME, bem como a configuração na própria aplicação de função.
 
Navegue para **a App função** e selecione a sua aplicação de função. Clique em **funcionalidades de Plataforma** e em **Rede** clique em **domínios personalizados**.

![lâmina de aplicação de função](./media/dns-custom-domain/functionapp.png)

Note o url atual na lâmina **de domínios personalizados,** este endereço é usado como pseudónimo para o registo DNS criado.

![lâmina de domínio personalizada](./media/dns-custom-domain/functionshostname.png)

Navegue para a sua Zona DNS e clique **em + Conjunto de registos.** Preencha as seguintes informações na lâmina **de conjunto de registos Add** e clique em **OK** para criá-la.

|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | myfunctionapp        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está a usar um pseudónimo.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | adatumfunction.azurewebsites.net        | O nome DNS que está a criar é para, neste exemplo, o nome adatumfunction.azurewebsites.net DNS fornecido por padrão à aplicação de função.        |

Volte para a sua aplicação de função, clique nas **funcionalidades da Plataforma** e em **Rede** clique em **domínios personalizados**, em seguida, em **Hostnames personalizados** clique **+ Adicionar nome de anfitrião**.

Na lâmina **de nome de anfitrião** Adicionar, introduza o registo CNAME no campo de texto do **nome anfitrião** e clique em **Validar**. Se o registo for encontrado, aparece o botão **addname.** Clique **em Adicionar nome de anfitrião** para adicionar o pseudónimo.

![aplicativos de função adicionar lâmina de nome anfitrião](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Endereço IP público

Para configurar um domínio personalizado para serviços que utilizem um recurso de endereço IP público, como Application Gateway, Load Balancer, Cloud Service, Resource Manager VMs e, Classic VMs, é utilizado um registo A.

Navegue para o endereço IP público **em rede,**  >  selecione o recurso IP público e clique em **Configuração**. Notiem o endereço IP mostrado.

![lâmina ip público](./media/dns-custom-domain/publicip.png)

Navegue para a sua Zona DNS e clique **em + Conjunto de registos.** Preencha as seguintes informações na lâmina **de conjunto de registos Add** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | mywebserver        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | A        | Utilize um registo A já que o recurso é um endereço IP.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Endereço IP     | `<your ip address>`       | O endereço IP público.|

![criar um recorde A](./media/dns-custom-domain/arecord.png)

Assim que o registo A for criado, corra `nslookup` para validar o registo.

![ip dns públicos olhar](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Serviço de Aplicações (Web Apps)

Os seguintes passos levam-no através da configuração de um domínio personalizado para uma aplicação web de serviço de aplicações.

Navegue para o **Serviço de Aplicações** e selecione o recurso que está a configurar um nome de domínio personalizado e clique em **domínios personalizados**.

Note o url atual na lâmina **de domínios personalizados,** este endereço é usado como pseudónimo para o registo DNS criado.

![lâmina de domínios personalizados](./media/dns-custom-domain/url.png)

Navegue para a sua Zona DNS e clique **em + Conjunto de registos.** Preencha as seguintes informações na lâmina **de conjunto de registos Add** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | mywebserver        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está a usar um pseudónimo. Se o recurso usasse um endereço IP, seria utilizado um registo A.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | webserver.azurewebsites.net        | O nome DNS que está a criar é para, neste exemplo, o nome webserver.azurewebsites.net DNS fornecido por padrão para a aplicação web.        |


![criar um registo CNAME](./media/dns-custom-domain/createcnamerecord.png)

Volte para o serviço de aplicações configurado para o nome de domínio personalizado. Clique **em domínios personalizados** e, em seguida, clique em **Hostnames**. Para adicionar o registo CNAME que criou, clique **em + Adicione o nome de anfitrião**.

![Screenshot que realça o botão + Adicionar nome de anfitrião.](./media/dns-custom-domain/figure1.png)

Uma vez concluído o processo, **executar nslookup** para validar a resolução de nomes está funcionando.

![figura 1](./media/dns-custom-domain/finalnslookup.png)

Para saber mais sobre o mapeamento de um domínio personalizado para o Serviço de Aplicações, visite [Mapear um nome DNS personalizado existente para Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Para aprender a migrar um nome DNS ativo, consulte [Migrar um nome DNS ativo para o Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Se precisar de adquirir um domínio personalizado, visite [Compre um nome de domínio personalizado para Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) para saber mais sobre os domínios do Serviço de Aplicações.

## <a name="blob-storage"></a>Armazenamento de blobs

Os seguintes passos levam-no através da configuração de um registo CNAME para uma conta de armazenamento de bolhas utilizando o método de asverificar. Este método garante que não há tempo de inatividade.

Navegue para  >  **contas de armazenamento,** selecione a sua conta de armazenamento e clique no domínio **personalizado**. Notate o FQDN no passo 2, este valor é usado para criar o primeiro recorde CNAME

![domínio personalizado de armazenamento de blob](./media/dns-custom-domain/blobcustomdomain.png)

Navegue para a sua Zona DNS e clique **em + Conjunto de registos.** Preencha as seguintes informações na lâmina **de conjunto de registos Add** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | asverify.mystorageaccount        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está a usar um pseudónimo.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | O nome DNS que está a criar é para, neste exemplo, o nome dns asverify.adatumfunctiona9ed.blob.core.windows.net fornecido por padrão na conta de armazenamento.        |

Volte para a sua conta de armazenamento clicando em  >  **Contas de Armazenamento,** selecione a sua conta de armazenamento e clique no domínio **personalizado.** Digite o pseudónimo que criou sem o prefixo de asverificar na caixa de texto, verifique **a validação indireta do CNAME** e clique em **Guardar**. Uma vez concluído este passo, volte à sua zona de DNS e crie um registo CNAME sem o prefixo de asverificar.  Depois desse ponto, é seguro apagar o registo CNAME com o prefixo cdnver.

![Screenshot que mostra a página de Domínio Personalizado.](./media/dns-custom-domain/indirectvalidate.png)

Validar a resolução do DNS executando `nslookup`

Para saber mais sobre mapear um domínio personalizado para um ponto final de armazenamento de bolhas visite [Configure um nome de domínio personalizado para o seu ponto final de armazenamento Blob](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>CDN do Azure

Os seguintes passos levam-no através da configuração de um disco CNAME para um ponto final cdn utilizando o método cdnverify. Este método garante que não há tempo de inatividade.

Navegue em **Rede**  >  **perfis CDN,** selecione o seu perfil CDN.

Selecione o ponto final com o quais está a trabalhar e clique **em + domínio personalizado.** Note o **nome de anfitrião Endpoint** como este valor é o recorde que o recorde CNAME aponta para.

![Domínio personalizado CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navegue para a sua Zona DNS e clique **em + Conjunto de registos.** Preencha as seguintes informações na lâmina **de conjunto de registos Add** e clique em **OK** para criá-la.

|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | cdnverify.mycdnendpoint        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está a usar um pseudónimo.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | O nome DNS que está a criar é para, neste exemplo, o nome dns cdnverify.adatumcdnendpoint.azureedge.net fornecido por padrão na conta de armazenamento.        |

Volte a navegar para o seu ponto final cdn clicando em Perfis CDN **de rede** e  >  selecione o seu perfil CDN. Clique **+ Domínio personalizado** e introduza o seu pseudónimo de registo CNAME sem o prefixo cdnver e clique em **Adicionar**.

Uma vez concluído este passo, volte à sua zona de DNS e crie um registo CNAME sem o prefixo cdnver.  Depois desse ponto, é seguro apagar o registo CNAME com o prefixo cdnver. Para obter mais informações sobre a CDN e como configurar um domínio personalizado sem a etapa de registo intermédio visite o [conteúdo do Map Azure CDN para um domínio personalizado.](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar DNS invertidos para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).

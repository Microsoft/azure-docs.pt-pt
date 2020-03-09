---
title: Integrar o Azure DNS com os seus recursos Azure - Azure DNS
description: Neste artigo, aprenda a utilizar o Azure DNS para fornecer DNS para os seus recursos Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: d84a7a908bd3bb5cfb2958a617be437f3b6b154e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357677"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Utilize o Azure DNS para fornecer configurações de domínio personalizadas para um serviço Azure

O Azure DNS fornece DNS para um domínio personalizado para qualquer um dos seus recursos Azure que suportedomínios personalizados ou que tenham um nome de domínio totalmente qualificado (FQDN). Um exemplo é que tem uma aplicação web Azure e quer que os seus utilizadores acedam a ela usando contoso.com, ou www\.contoso.com como um FQDN. Este artigo acompanha-o através da configuração do seu serviço Azure com o Azure DNS para utilizar domínios personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o Azure DNS para o seu domínio personalizado, deve primeiro delegar o seu domínio no Azure DNS. Visite [o Delegado de Um domínio ao DNS Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os seus servidores de nome para delegação. Uma vez que o seu domínio é delegado à sua zona DeNS Azure, você é capaz de configurar os registos DNS necessários.

Pode configurar uma vaidade ou domínio personalizado para aplicações de [função Azure,](#azure-function-app) [endereços IP públicos,](#public-ip-address) [Serviço de Aplicações (Aplicações Web),](#app-service-web-apps) [armazenamento blob](#blob-storage)e [CDN Azure](#azure-cdn).

## <a name="azure-function-app"></a>App de função Azure

Para configurar um domínio personalizado para aplicações de função Azure, é criado um registo CNAME, bem como uma configuração na própria aplicação de funções.
 
Navegue para **'App' e** selecione a sua aplicação de funções. Clique nas **funcionalidades da Plataforma** e em **Networking** clique em **domínios personalizados**.

![lâmina de aplicativo de função](./media/dns-custom-domain/functionapp.png)

Note o url atual na lâmina de **domínios Personalizados,** este endereço é usado como pseudónimo para o registo DNS criado.

![lâmina de domínio personalizado](./media/dns-custom-domain/functionshostname.png)

Navegue para a sua Zona DNS e clique **em + conjunto de registos**. Preencha as seguintes informações sobre a lâmina de conjunto de **registos Adicionar** e clique em **OK** para criá-la.

|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | myfunctionapp        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está usando um pseudónimo.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | adatumfunction.azurewebsites.net        | O nome DNS para o que está a criar, neste exemplo, é o nome dNS adatumfunction.azurewebsites.net fornecido por padrão à aplicação de funções.        |

Navegue de volta para a sua aplicação de função, clique em **funcionalidades da Plataforma**, e em **Networking** clique em **domínios personalizados**e, em seguida, em nomes de **anfitriões personalizados** clique **+ Adicione nome de anfitrião**.

Na lâmina **Add hostname,** introduza o registo CNAME no campo de texto do nome de **anfitrião** e clique em **Validar**. Se o registo for encontrado, aparece o botão **Adicionar nome de anfitrião.** Clique em **Adicionar nome de anfitrião** para adicionar o pseudónimo.

![aplicativos de função adicionar lâmina de nome anfitrião](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Endereço IP público

Para configurar um domínio personalizado para serviços que utilizam um recurso de endereço IP público, como Application Gateway, Load Balancer, Cloud Service, Resource Manager VMs e, Classic VMs, é utilizado um registo A.

Navegue para **networking** > **endereço IP público,** selecione o recurso IP público e clique na **configuração**. Repare no endereço IP mostrado.

![lâmina ip pública](./media/dns-custom-domain/publicip.png)

Navegue para a sua Zona DNS e clique **em + conjunto de registos**. Preencha as seguintes informações sobre a lâmina de conjunto de **registos Adicionar** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | mywebserver        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | A        | Utilize um registo A, uma vez que o recurso é um endereço IP.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Endereço IP     | `<your ip address>`       | O endereço IP público.|

![criar um registo A](./media/dns-custom-domain/arecord.png)

Uma vez criado o disco A, execute `nslookup` para validar as resoluções do registo.

![público ip dns olharup](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Serviço de Aplicativos (Web Apps)

Os seguintes passos levam-no através da configuração de um domínio personalizado para uma aplicação web de serviço de aplicação.

Navegue para o Serviço de **Aplicações** e selecione o recurso que está a configurar um nome de domínio personalizado e clique em **domínios personalizados**.

Note o url atual na lâmina de **domínios Personalizados,** este endereço é usado como pseudónimo para o registo DNS criado.

![lâmina de domínios personalizados](./media/dns-custom-domain/url.png)

Navegue para a sua Zona DNS e clique **em + conjunto de registos**. Preencha as seguintes informações sobre a lâmina de conjunto de **registos Adicionar** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | mywebserver        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está usando um pseudónimo. Se o recurso usasse um endereço IP, seria utilizado um registo A.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | webserver.azurewebsites.net        | O nome DNS para o que está a criar, neste exemplo, é o nome dNS webserver.azurewebsites.net fornecido por padrão para a aplicação web.        |


![criar um registo CNAME](./media/dns-custom-domain/createcnamerecord.png)

Navegue de volta para o serviço de aplicações que está configurado para o nome de domínio personalizado. Clique em **domínios personalizados**e, em seguida, clique em **Nomes de Anfitriões**. Para adicionar o disco CNAME que criou, clique em **+ Adicione o nome de anfitrião**.

![figura 1](./media/dns-custom-domain/figure1.png)

Uma vez concluído o processo, a execução **de nslookup** para validar a resolução de nomeestá a funcionar.

![figura 1](./media/dns-custom-domain/finalnslookup.png)

Para saber mais sobre mapear um domínio personalizado para o App Service, visite [mapeie um nome dNS personalizado existente para Aplicações Web Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Para aprender a migrar um nome DNS ativo, consulte [Migrate um nome DNS ativo para o Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Se precisar de adquirir um domínio personalizado, visite Compre um nome de [domínio personalizado para Web Apps Azure](../app-service/manage-custom-dns-buy-domain.md) para saber mais sobre domínios do Serviço de Aplicações.

## <a name="blob-storage"></a>Armazenamento de blobs

Os seguintes passos levam-no através da configuração de um registo CNAME para uma conta de armazenamento blob utilizando o método de verificação. Este método garante que não há tempo de inatividade.

Navegue para **Guardar** > Contas de **Armazenamento,** selecione a sua conta de armazenamento e clique no **domínio Personalizado**. Note o FQDN sob o passo 2, este valor é usado para criar o primeiro disco CNAME

![domínio personalizado de armazenamento blob](./media/dns-custom-domain/blobcustomdomain.png)

Navegue para a sua Zona DNS e clique **em + conjunto de registos**. Preencha as seguintes informações sobre a lâmina de conjunto de **registos Adicionar** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | asverify.mystorageaccount        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está usando um pseudónimo.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | O nome DNS para o que está a criar, neste exemplo, é o nome asverify.adatumfunctiona9ed.blob.core.windows.net DNS fornecido por padrão à conta de armazenamento.        |

Navegue de volta para a sua conta de armazenamento clicando em Contas de **Armazenamento** > **Armazenamento,** selecione a sua conta de armazenamento e clique no **domínio Personalizado**. Digite o pseudónimo que criou sem o prefixo assionna na caixa de texto, verifique **Use a validação cname indireta e clique em **Guardar**. Uma vez concluído este passo, volte à sua zona DNS e crie um registo CNAME sem o prefixo de assuma.  Depois disso, é seguro eliminar o registo CNAME com o prefixo de cdncheck.

![domínio personalizado de armazenamento blob](./media/dns-custom-domain/indirectvalidate.png)

Validar a resolução dNS executando `nslookup`

Para saber mais sobre mapear um domínio personalizado para um ponto final de armazenamento blob, [configure um nome de domínio personalizado para o seu ponto final](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json) de armazenamento Blob

## <a name="azure-cdn"></a>CDN do Azure

Os seguintes passos levam-no através da configuração de um registo CNAME para um ponto final da CDN utilizando o método de cdncheck. Este método garante que não há tempo de inatividade.

Navegue para **Networking** > **Perfis CDN,** selecione o seu perfil CDN.

Selecione o ponto final com que está a trabalhar e clique **em + Domínio personalizado**. Note o nome de **anfitrião endpoint** como este valor é o registo a que o recorde CNAME aponta.

![Domínio personalizado CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navegue para a sua Zona DNS e clique **em + conjunto de registos**. Preencha as seguintes informações sobre a lâmina de conjunto de **registos Adicionar** e clique em **OK** para criá-la.

|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | cdnverify.mycdnendpoint        | Este valor juntamente com a etiqueta de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registo CNAME está usando um pseudónimo.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade TTL     | Horas        | As horas são usadas como a medição do tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | O nome DNS para o que está a criar, neste exemplo, é o nome cdnverify.adatumcdnendpoint.azureedge.net DNS fornecido por padrão à conta de armazenamento.        |

Navegue de volta ao seu ponto final do CDN clicando em **Networking** > **Perfis CDN**, e selecione o seu perfil CDN. Clique **em + domínio personalizado** e introduza o seu pseudónimo de registo CNAME sem o prefixo de cdncheck e clique em **Adicionar**.

Uma vez concluído este passo, volte à sua zona DNS e crie um registo CNAME sem o prefixo de cdncheck.  Depois disso, é seguro eliminar o registo CNAME com o prefixo de cdncheck. Para obter mais informações sobre a CDN e como configurar um domínio personalizado sem a etapa de registo intermédio visite o [conteúdo do Map Azure CDN para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar dNS invertidos para serviços hospedados em Azure](dns-reverse-dns-for-azure-services.md).

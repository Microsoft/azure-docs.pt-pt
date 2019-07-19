---
title: Integrar o DNS do Azure com seus recursos do Azure
description: Saiba como usar o DNS do Azure para fornecer o DNS para os recursos do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 051aabed758f80208549cf64bf5d74b1fecfbe75
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854164"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Usar o DNS do Azure para fornecer configurações de domínio personalizadas para um serviço do Azure

O DNS do Azure fornece o DNS para um domínio personalizado para qualquer um dos seus recursos do Azure que dão suporte a domínios personalizados ou que têm um FQDN (nome de domínio totalmente qualificado). Um exemplo é que você tem um aplicativo Web do Azure e deseja que os usuários o acessem usando o contoso.com ou o contoso.com\.da Web como um FQDN. Este artigo orienta você pela configuração do serviço do Azure com o DNS do Azure para usar domínios personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o DNS do Azure para seu domínio personalizado, primeiro você deve delegar seu domínio ao DNS do Azure. Visite [delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar seus servidores de nomes para delegação. Depois que o domínio for delegado à zona DNS do Azure, você poderá configurar os registros DNS necessários.

Você pode configurar um domínio intuitivo ou personalizado para [aplicativos de funções do Azure](#azure-function-app), [endereços IP públicos](#public-ip-address), [serviço de aplicativo (aplicativos Web)](#app-service-web-apps), [armazenamento](#blob-storage)de BLOBs e [CDN do Azure](#azure-cdn).

## <a name="azure-function-app"></a>Aplicativo de funções do Azure

Para configurar um domínio personalizado para os aplicativos de funções do Azure, um registro CNAME é criado, bem como a configuração no próprio aplicativo de funções.
 
Navegue até **aplicativo de funções** e selecione seu aplicativo de funções. Clique em **recursos de plataforma** e, em **rede** , clique em **domínios personalizados**.

![folha do aplicativo de funções](./media/dns-custom-domain/functionapp.png)

Observe a URL atual na folha **domínios personalizados** , esse endereço é usado como o alias para o registro DNS criado.

![folha de domínio personalizado](./media/dns-custom-domain/functionshostname.png)

Navegue até a zona DNS e clique em **+ conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-la.

|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Nome     | myfunctionapp        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Usar um registro CNAME está usando um alias.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como medição de tempo         |
|Alias     | adatumfunction.azurewebsites.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS adatumfunction.azurewebsites.net fornecido por padrão para o aplicativo de funções.        |

Navegue de volta para seu aplicativo de funções, clique em **recursos da plataforma**e, em **rede** , clique em **domínios personalizados**e, em **nomes de host personalizados** , clique em **+ adicionar nome do host**.

Na folha **adicionar nome do host** , insira o registro CNAME no campo de texto **nome do host** e clique em **validar**. Se o registro for encontrado, o botão **adicionar nome de host** será exibido. Clique em **adicionar nome do host** para adicionar o alias.

![folha de adicionar nome de host aos aplicativos de funções](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Endereço IP público

Para configurar um domínio personalizado para serviços que usam um recurso de endereço IP público, como gateway de aplicativo, Load Balancer, serviço de nuvem, VMs do Gerenciador de recursos e VMs clássicas, um registro A é usado.

Navegue até **rede** > **endereço IP público**, selecione o recurso IP público e clique em **configuração**. Notate o endereço IP mostrado.

![folha IP público](./media/dns-custom-domain/publicip.png)

Navegue até a zona DNS e clique em **+ conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-la.


|Propriedade  |Valor  |Descrição  |
|---------|---------|---------|
|Nome     | MyWebServer        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | A        | Use um registro A, pois o recurso é um endereço IP.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como medição de tempo         |
|Endereço IP     | `<your ip address>`       | O endereço IP público.|

![criar um registro A](./media/dns-custom-domain/arecord.png)

Depois que o registro a é criado, `nslookup` execute para validar a resolução do registro.

![pesquisa de DNS de IP público](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Serviço de aplicativo (aplicativos Web)

As etapas a seguir o levarão para a configuração de um domínio personalizado para um aplicativo Web do serviço de aplicativo.

Navegue até o **serviço de aplicativo** e selecione o recurso que você está configurando um nome de domínio personalizado e clique em **domínios personalizados**.

Observe a URL atual na folha **domínios personalizados** , esse endereço é usado como o alias para o registro DNS criado.

![folha domínios personalizados](./media/dns-custom-domain/url.png)

Navegue até a zona DNS e clique em **+ conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-la.


|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Nome     | MyWebServer        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Usar um registro CNAME está usando um alias. Se o recurso tiver usado um endereço IP, um registro A será usado.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como medição de tempo         |
|Alias     | webserver.azurewebsites.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS webserver.azurewebsites.net fornecido por padrão para o aplicativo Web.        |


![criar um registro CNAME](./media/dns-custom-domain/createcnamerecord.png)

Navegue de volta para o serviço de aplicativo que está configurado para o nome de domínio personalizado. Clique em **domínios personalizados**e em **nomes de host**. Para adicionar o registro CNAME que você criou, clique em **+ adicionar nome do host**.

![figura 1](./media/dns-custom-domain/figure1.png)

Quando o processo for concluído, execute **nslookup** para validar que a resolução de nomes está funcionando.

![figura 1](./media/dns-custom-domain/finalnslookup.png)

Para saber mais sobre como mapear um domínio personalizado para o serviço de aplicativo, visite [mapear um nome DNS personalizado existente para aplicativos Web do Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Para saber como migrar um nome DNS ativo, consulte [migrar um nome DNS ativo para Azure app serviço](../app-service/manage-custom-dns-migrate-domain.md).

Se precisar comprar um domínio personalizado, acesse [comprar um nome de domínio personalizado para aplicativos Web do Azure](../app-service/manage-custom-dns-buy-domain.md) para saber mais sobre domínios do serviço de aplicativo.

## <a name="blob-storage"></a>Armazenamento de blobs

As etapas a seguir o orientarão na configuração de um registro CNAME para uma conta de armazenamento de BLOBs usando o método asverify. Esse método garante que não haja nenhum tempo de inatividade.

Navegue até **armazenamento** > **contas**de armazenamento, selecione sua conta de armazenamento e clique em **domínio personalizado**. Notate o FQDN na etapa 2, esse valor é usado para criar o primeiro registro CNAME

![domínio personalizado do armazenamento de BLOBs](./media/dns-custom-domain/blobcustomdomain.png)

Navegue até a zona DNS e clique em **+ conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-la.


|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Nome     | asverify.mystorageaccount        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Usar um registro CNAME está usando um alias.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como medição de tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS asverify.adatumfunctiona9ed.blob.core.windows.net fornecido por padrão para a conta de armazenamento.        |

Navegue de volta para sua conta de armazenamento **clicando em** > **contas de armazenamento**de armazenamento, selecione sua conta de armazenamento e clique em **domínio personalizado**. Digite o alias que você criou sem o prefixo asverify na caixa de texto, marque * * usar validação de CNAME indireto e clique em **salvar**. Após a conclusão desta etapa, retorne à zona DNS e crie um registro CNAME sem o prefixo asverify.  Depois desse ponto, você é seguro excluir o registro CNAME com o prefixo cdnverify.

![domínio personalizado do armazenamento de BLOBs](./media/dns-custom-domain/indirectvalidate.png)

Validar a resolução de DNS executando`nslookup`

Para saber mais sobre como mapear um domínio personalizado para um ponto de extremidade de armazenamento de BLOB, visite [configurar um nome de domínio personalizado para o ponto de extremidade de armazenamento de blob](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>CDN do Azure

As etapas a seguir o orientarão na configuração de um registro CNAME para um ponto de extremidade CDN usando o método cdnverify. Esse método garante que não haja nenhum tempo de inatividade.

Navegue até **rede** > **perfis de CDN**, selecione seu perfil CDN.

Selecione o ponto de extremidade com o qual você está trabalhando e clique em **+ domínio personalizado**. Observe o **nome do host do ponto de extremidade** como esse valor é o registro para o qual o registro CNAME aponta.

![Domínio personalizado da CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navegue até a zona DNS e clique em **+ conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-la.

|Propriedade  |Value  |Descrição  |
|---------|---------|---------|
|Nome     | cdnverify.mycdnendpoint        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Type     | CNAME        | Usar um registro CNAME está usando um alias.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | As horas são usadas como medição de tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS cdnverify.adatumcdnendpoint.azureedge.net fornecido por padrão para a conta de armazenamento.        |

Navegue de volta para o ponto de extremidade da CDN clicando em **rede** > **perfis de CDN**e selecione seu perfil CDN. Clique em **+ domínio personalizado** e insira seu alias de registro CNAME sem o prefixo cdnverify e clique em **Adicionar**.

Após a conclusão desta etapa, retorne à zona DNS e crie um registro CNAME sem o prefixo cdnverify.  Depois desse ponto, você é seguro excluir o registro CNAME com o prefixo cdnverify. Para obter mais informações sobre a CDN e como configurar um domínio personalizado sem a etapa de registro intermediária, visite [mapear conteúdo da CDN do Azure para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Passos Seguintes

Saiba como [Configurar o DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).

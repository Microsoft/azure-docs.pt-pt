---
title: Gerir a expiração dos conteúdos web no Azure CDN Microsoft Docs
description: Saiba como gerir a expiração de Azure Web Apps/Cloud Services, ASP.NET ou IIS em Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240975"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Manage expiration of web content in Azure CDN (Gerir a expiração do conteúdo Web na CDN do Azure)
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Os ficheiros de servidores web de origem acessível ao público podem ser em cache na Rede de Entrega de Conteúdos Azure (CDN) até que o seu tempo de vida (TTL) desemtra. O TTL é determinado pelo `Cache-Control` cabeçalho na resposta HTTP do servidor de origem. Este artigo descreve como definir `Cache-Control` cabeçalhos para a funcionalidade de Aplicações Web do Microsoft Azure App Service, Azure Cloud Services, ASP.NET aplicações e sites de Serviços de Informação da Internet (IIS), todos configurados da mesma forma. Pode definir o `Cache-Control` cabeçalho utilizando ficheiros de configuração ou programáticamente. 

Também pode controlar as definições de cache a partir do portal Azure definindo [as regras de cache CDN](cdn-caching-rules.md). Se criar uma ou mais regras de cache e definir o seu comportamento de cache de cache de **substituição** ou **bypass,** as definições de cache fornecidas pela origem discutidas neste artigo são ignoradas. Para obter informações sobre conceitos gerais de caching, consulte [como funciona o caching.](cdn-how-caching-works.md)

> [!TIP]
> Pode optar por não definir TTL num ficheiro. Neste caso, o Azure CDN aplica automaticamente um TTL predefinido de sete dias, a menos que tenha configurado regras de caching no portal Azure. Este TTL predefinido aplica-se apenas às otimizações gerais de entrega web. Para grandes otimizações de ficheiros, o TTL padrão é um dia, e para otimizações de streaming de mídia, o TTL padrão é de um ano.
> 
> Para obter mais informações sobre como o Azure CDN funciona para acelerar o acesso a ficheiros e outros recursos, consulte [a Visão Geral da Rede de Entrega de Conteúdos Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definição de cabeçalhos Cache-Control utilizando regras de caching CDN
O método preferido para configurar o cabeçalho de um servidor web `Cache-Control` é utilizar as regras de caching no portal Azure. Para obter mais informações sobre as regras de caching CDN, consulte [o comportamento do caching do Control Azure CDN com as regras de caching](cdn-caching-rules.md).

> [!NOTE] 
> As regras de caching estão disponíveis apenas para **Azure CDN Standard da Verizon** e **Azure CDN Standard a partir de** perfis Akamai. Para **o Azure CDN Premium a partir de** perfis Verizon, deve utilizar o motor de [regras Azure CDN](./cdn-verizon-premium-rules-engine.md) no portal **Manage** para obter funcionalidades semelhantes.

**Para navegar na página de regras do caching CDN** :

1. No portal Azure, selecione um perfil CDN e, em seguida, selecione o ponto final para o servidor web.

1. No painel esquerdo, em Definições, selecione **Regras de colocação em cache** .

   ![Botão de regras de caching CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache** .

   ![Página de caching CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Para definir os cabeçalhos de Cache-Control de um servidor web utilizando regras globais de caching:**

1. De acordo com **as regras globais de caching** , desempenhar **o comportamento de caching de cordas de consulta** para ignorar as cordas de **consulta** e definir o comportamento **de Caching** para **Override** .
      
1. Para **a duração de validade da Cache** , introduza 3600 na caixa **Seconds** ou 1 na caixa **Horas.** 

   ![CdN global caching regras exemplo](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Esta regra global de cache define uma duração de cache de uma hora e afeta todos os pedidos para o ponto final. Substitui qualquer `Cache-Control` cabeçalho ou `Expires` HTTP que são enviados pelo servidor de origem especificado pelo ponto final.   

1. Selecione **Guardar** .

**Para definir os cabeçalhos de Cache-Control de um ficheiro de servidor web utilizando regras de caching personalizadas:**

1. De acordo com **as regras de caching personalizado,** crie duas condições de jogo:

     a. Para a primeira condição de jogo, desacordo **a condição de jogo** para **Path** e `/webfolder1/*` **introduza o valor de Match** . **Desentuê-lo** para **substituir** e introduza 4 na caixa **Dias.**

     b. Para a segunda condição de jogo, desacordo **a condição de jogo** para **Path** e `/webfolder1/file1.txt` **introduza o valor do Match** . **Desentuê-lo** para **substituir** e introduza 2 na caixa **Days.**

    ![CdN costume caching regras exemplo](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache personalizada define uma duração de cache de quatro horas para quaisquer ficheiros `/webfolder1` na pasta no servidor de origem especificado pelo seu ponto final. A segunda regra substitui apenas a primeira regra do `file1.txt` ficheiro e define uma duração de cache de duas horas para o mesmo.

1. Selecione **Guardar** .


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Definição de cabeçalhos Cache-Control utilizando ficheiros de configuração
Para conteúdos estáticos, como imagens e folhas de estilo, pode controlar a frequência de atualização modificando os ficheiros de configuração **applicationHost.config** ou **Web.config** para a sua aplicação web. Para definir o `Cache-Control` cabeçalho para o seu conteúdo, utilize o `<system.webServer>/<staticContent>/<clientCache>` elemento em qualquer ficheiro.

### <a name="using-applicationhostconfig-files"></a>Utilização de ficheiros ApplicationHost.config
O **ficheiroApplicationHost.config** é o ficheiro raiz do sistema de configuração IIS. As definições de configuração num ficheiro **ApplicationHost.config** afetam todas as aplicações no site, mas são ultrapassadas pelas definições de quaisquer **ficheirosWeb.config** que existam para uma aplicação web.

### <a name="using-webconfig-files"></a>Utilização de ficheiros Web.config
Com um ficheiro **Web.config,** pode personalizar a forma como toda a sua aplicação web ou um diretório específico na sua aplicação web se comporta. Normalmente, tem pelo menos um **ficheiroWeb.config** na pasta raiz da sua aplicação web. Para cada **Web.config** ficheiro numa pasta específica, as definições de configuração afetam tudo nessa pasta e nas suas sub-dobradeiras, a menos que sejam ultrapassadas ao nível da sub-fase por outro ficheiro **Web.config.** 

Por exemplo, pode definir um `<clientCache>` elemento num ficheiro **Web.config** na pasta raiz da sua aplicação web para cache de todo o conteúdo estático na sua aplicação web durante três dias. Também pode adicionar um ficheiro **Web.config** numa sub-dobradeira com mais conteúdo variável (por exemplo, `\frequent` ) e definir o seu elemento para cache o conteúdo `<clientCache>` da sub-dobradeira durante seis horas. O resultado líquido é que o conteúdo em todo o site é cached por três dias, exceto para qualquer conteúdo no `\frequent` diretório, que é em cache por apenas seis horas.  

O exemplo do ficheiro de configuração XML que se segue mostra como definir o `<clientCache>` elemento para especificar uma idade máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para utilizar o atributo **cacheControlMaxAge,** tem de definir o valor do atributo **cacheControlMode** a `UseMaxAge` . Esta definição fez com que o cabeçalho e a diretiva HTTP `Cache-Control: max-age=<nnn>` fossem adicionados à resposta. O formato do valor timepan para o atributo **cacheControlMaxAge** é `<days>.<hours>:<min>:<sec>` . O seu valor é convertido em segundos e é utilizado como valor da `Cache-Control` `max-age` diretiva. Para obter mais informações sobre o `<clientCache>` elemento, consulte [a Cache \<clientCache> do Cliente. ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)  

## <a name="setting-cache-control-headers-programmatically"></a>Definição de cabeçalhos Cache-Control programáticamente
Para aplicações ASP.NET, controla o comportamento de cache cdN programáticamente definindo a propriedade **HttpResponse.Cache** da API .NET. Para obter informações sobre a propriedade **HttpResponse.Cache,** consulte [httpResponse.Cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) e [HttpCachePolicy Class](/dotnet/api/system.web.httpcachepolicy).  

Para programaticamente cache conteúdo de aplicação em ASP.NET, siga estes passos:
   1. Verifique se o conteúdo está marcado como cacheable, definindo `HttpCacheability` para `Public` . 
   1. Desa estaleie um validador de cache chamando um dos `HttpCachePolicy` seguintes métodos:
      - Chamada `SetLastModified` para definir um valor de fixação de tempo para o `Last-Modified` cabeçalho.
      - Chamada `SetETag` para definir um valor para o `ETag` cabeçalho.
   1. Opcionalmente, especifique um tempo de validade da cache, chamando `SetExpires` para definir um valor para o `Expires` cabeçalho. Caso contrário, aplicam-se as heurísticas de cache padrão descritas anteriormente neste documento.

Por exemplo, para cache conteúdo durante uma hora, adicione o seguinte código C# :  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testar o cabeçalho Cache-Control
Pode verificar facilmente as definições de TTL do seu conteúdo web. Com as [ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de desenvolvimento do seu navegador, teste que o seu conteúdo web inclui o `Cache-Control` cabeçalho de resposta. Também pode utilizar uma ferramenta como **wget,** [Carteiro](https://www.getpostman.com/)ou [Violinista](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Leia detalhes sobre o elemento **clienteCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação para a Propriedade **HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Leia a documentação para a **Aula de Política httpCache**](/dotnet/api/system.web.httpcachepolicy)  
* [Conheça os conceitos de caching](cdn-how-caching-works.md)

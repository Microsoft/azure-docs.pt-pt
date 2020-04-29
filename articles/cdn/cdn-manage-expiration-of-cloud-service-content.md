---
title: Gerir a expiração de conteúdos web no Azure CDN [ Microsoft Docs
description: Saiba como gerir a expiração dos conteúdos azure Web Apps/Cloud, ASP.NET ou IIS no Azure CDN.
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
ms.topic: article
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: 4598e6cee6ffbaaeb2a99727842fcd17fe0046c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260569"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Manage expiration of web content in Azure CDN (Gerir a expiração do conteúdo Web na CDN do Azure)
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Os ficheiros de servidores web de origem acessível ao público podem ser guardados na Rede de Entrega de Conteúdos Azure (CDN) até que o seu tempo de vida (TTL) decorrido. O TTL é `Cache-Control` determinado pelo cabeçalho na resposta HTTP do servidor de origem. Este artigo descreve `Cache-Control` como definir cabeçalhos para a funcionalidade web Apps do Microsoft Azure App Service, Azure Cloud Services, ASP.NET aplicações e sites de Serviços de Informação de Internet (IIS), todos configurados da mesma forma. Pode definir `Cache-Control` o cabeçalho utilizando ficheiros de configuração ou programáticamente. 

Também pode controlar as definições de cache do portal Azure, definindo [regras de cache CDN](cdn-caching-rules.md). Se criar uma ou mais regras de cache e definir o seu comportamento de cache de **sobreposição** ou **bypass,** as definições de cache fornecidas pela origem discutidas neste artigo são ignoradas. Para obter informações sobre conceitos gerais de cache, veja como funciona o [cache.](cdn-how-caching-works.md)

> [!TIP]
> Pode optar por definir nenhuma TTL num ficheiro. Neste caso, o Azure CDN aplica automaticamente um TTL padrão de sete dias, a menos que tenha estabelecido regras de cache no portal Azure. Este TTL padrão aplica-se apenas às otimizações gerais de entrega web. Para grandes otimizações de ficheiros, o TTL padrão é um dia, e para otimizações de streaming de mídia, o TTL padrão é de um ano.
> 
> Para obter mais informações sobre como o Azure CDN trabalha para acelerar o acesso a ficheiros e outros recursos, consulte a [Visão Geral da Rede de Entrega de Conteúdos Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definição de cabeçalhos de controlo de cache utilizando regras de cache cdn
O método preferido para definir `Cache-Control` o cabeçalho de um servidor web é usar regras de cache no portal Azure. Para obter mais informações sobre as regras de cache da CDN, consulte [controlazur e cDN comportamento de cache com regras](cdn-caching-rules.md)de cache .

> [!NOTE] 
> As regras de cache estão disponíveis apenas para **o Azure CDN Standard da Verizon** e **Azure CDN Standard a partir dos** perfis da Akamai. Para **o Azure CDN Premium a partir de** perfis Verizon, deve utilizar o motor de regras [Azure CDN](cdn-rules-engine.md) no portal **Manage** para funcionalidades semelhantes.

**Para navegar para a página de regras de cache cdN:**

1. No portal Azure, selecione um perfil CDN e, em seguida, selecione o ponto final para o servidor web.

1. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão de regras de cache CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página de cache CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Para definir os cabeçalhos de Cache-Control de um servidor web utilizando regras globais de cache:**

1. De acordo com **as regras globais**de cache, deset o comportamento de **cache de cordas de consulta** para ignorar cordas de **consulta** e definir o comportamento **de Caching** para **sobrepor**.
      
1. Para a duração de validade do **Cache,** introduza 3600 na caixa **Seconds** ou 1 na caixa **Horas.** 

   ![CDN regras globais de cache exemplo](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Esta regra global de cache define uma duração de cache de uma hora e afeta todos os pedidos até ao ponto final. Substitui quaisquer `Cache-Control` `Expires` cabeçalhos http ou HTTP que são enviados pelo servidor de origem especificado pelo ponto final.   

1. Selecione **Guardar**.

**Para definir os cabeçalhos de Cache-Control de um ficheiro web utilizando regras personalizadas de cacheching:**

1. De acordo com **as regras personalizadas de cache,** crie duas condições de jogo:

     a. Para a primeira condição de jogo, `/webfolder1/*` deset condição de **jogo** para **caminho** e introduza para o valor do **jogo**. Desloque o **comportamento de Caching** para **sobrepor-se** e introduza 4 na caixa **horas.**

     b. Para a segunda condição de jogo, `/webfolder1/file1.txt` descoloque a condição de **jogo** para o **Caminho** e introduza para o valor do **jogo**. Desloque o comportamento de **Caching** para **sobrepor-se** e introduza 2 na caixa **horas.**

    ![CDN regras de cache personalizado exemplo](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    A primeira regra de cache personalizada define uma duração `/webfolder1` de cache de quatro horas para quaisquer ficheiros na pasta no servidor de origem especificado pelo seu ponto final. A segunda regra substitui a `file1.txt` primeira regra apenas para o ficheiro e define uma duração de cache de duas horas para o mesmo.

1. Selecione **Guardar**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Definição de cabeçalhos de controlo de cache utilizando ficheiros de configuração
Para conteúdo estático, como imagens e folhas de estilo, pode controlar a frequência de atualização modificando os ficheiros de configuração **host.config** ou **Web.config** para a sua aplicação web. Para definir `Cache-Control` o cabeçalho para `<system.webServer>/<staticContent>/<clientCache>` o seu conteúdo, utilize o elemento em qualquer dos ficheiros.

### <a name="using-applicationhostconfig-files"></a>Utilização de ficheiros ApplicationHost.config
O ficheiro **ApplicationHost.config** é o ficheiro raiz do sistema de configuração IIS. As definições de configuração num ficheiro **ApplicationHost.config** afetam todas as aplicações do site, mas são ultrapassadas pelas definições de quaisquer ficheiros **Web.config** que existam para uma aplicação web.

### <a name="using-webconfig-files"></a>Usando ficheiros Web.config
Com um ficheiro **Web.config,** pode personalizar a forma como toda a sua aplicação web ou um diretório específico na sua aplicação web se comporta. Normalmente, tem pelo menos um ficheiro **Web.config** na pasta raiz da sua aplicação web. Para cada ficheiro **Web.config** numa pasta específica, as definições de configuração afetam tudo nessa pasta e nas suas subpastas, a menos que sejam sobremontadas ao nível da subpasta por outro ficheiro **Web.config.** 

Por exemplo, pode `<clientCache>` definir um elemento num ficheiro **Web.config** na pasta raiz da sua aplicação web para cache todos os conteúdos estáticos na sua aplicação web durante três dias. Também pode adicionar um ficheiro **Web.config** numa subpasta com `\frequent`conteúdo mais `<clientCache>` variável (por exemplo, ) e definir o seu elemento para cache o conteúdo da subpasta durante seis horas. O resultado líquido é que o conteúdo em todo o site é `\frequent` cachepor três dias, exceto para qualquer conteúdo no diretório, que está em cache por apenas seis horas.  

O exemplo de ficheiro de configuração `<clientCache>` XML seguinte mostra como definir o elemento para especificar uma idade máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para utilizar o atributo **cacheControlMaxAge,** deve definir o valor `UseMaxAge`do atributo **cacheControlMode** a . Esta definição fez com que `Cache-Control: max-age=<nnn>`o cabeçalho e a diretiva http, fossem adicionados à resposta. O formato do valor do timepan para `<days>.<hours>:<min>:<sec>`o atributo **cacheControlMaxAge** é . O seu valor é convertido em segundos `Cache-Control` `max-age` e é utilizado como valor da diretiva. Para mais informações sobre o `<clientCache>` elemento, consulte o cliente Cache do [clienteCache \<>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Definição de cabeçalhos de cache-control programática
Para ASP.NET aplicações, controla o comportamento de cache cdN programática, definindo a propriedade **HttpResponse.Cache** da API .NET. Para obter informações sobre a propriedade **HttpResponse.Cache,** consulte [httpResponse.Cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) e [HttpCachePolicy Class](/dotnet/api/system.web.httpcachepolicy).  

Para obter conteúdos de aplicação de cache programáticas em ASP.NET, siga estes passos:
   1. Verifique se o conteúdo está marcado `HttpCacheability` `Public`como cacheable definindo para . 
   1. Desloque um validador de `HttpCachePolicy` cache chamando um dos seguintes métodos:
      - Ligue `SetLastModified` para definir um valor `Last-Modified` de carimbo de tempo para o cabeçalho.
      - Chamada `SetETag` para definir um `ETag` valor para o cabeçalho.
   1. Opcionalmente, especifique um `SetExpires` tempo de validade `Expires` da cache, ligando para definir um valor para o cabeçalho. Caso contrário, aplicam-se as heurísticas de cache predefinidas descritas anteriormente neste documento.

Por exemplo, para cache conteúdo durante uma hora, adicione o seguinte código C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testar o cabeçalho cache-control
Pode verificar facilmente as definições de TTL do seu conteúdo web. Com [as ferramentas](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)de desenvolvimento do seu `Cache-Control` navegador, teste que o seu conteúdo web inclui o cabeçalho de resposta. Também pode utilizar uma ferramenta como **wget,** [Postman](https://www.getpostman.com/)ou [Fiddler](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Passos Seguintes
* [Leia detalhes sobre o elemento **clienteCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação para a **Propriedade HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Leia a documentação para a **Classe HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Saiba sobre conceitos de cache](cdn-how-caching-works.md)

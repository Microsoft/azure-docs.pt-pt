---
title: Gerir a expiração do conteúdo da web na CDN do Azure | Documentos da Microsoft
description: Saiba como gerir a expiração de conteúdo do Azure Web Apps/serviços Cloud, ASP.NET ou IIS na CDN do Azure.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: b070b302917d69e0145c1a10c90685b55aa4dcc2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540278"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Gerir a expiração do conteúdo da web na CDN do Azure
> [!div class="op_single_selector"]
> * [Conteúdo Web do Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Armazenamento de Blobs do Azure](cdn-manage-expiration-of-blob-content.md)
> 

Ficheiros a partir de servidores de web de origem acessível publicamente podem ser colocadas em cache na rede de entrega de conteúdos (CDN) do Azure até decorrida seus time-to-live (TTL). O valor de TTL é determinado pelo `Cache-Control` cabeçalho na resposta HTTP do servidor de origem. Este artigo descreve como definir `Cache-Control` cabeçalhos para a funcionalidade de aplicações Web do serviço de aplicações do Microsoft Azure, serviços Cloud do Azure, aplicativos ASP.NET e sites de serviços de informação Internet (IIS), que são configuradas da mesma forma. Pode definir o `Cache-Control` cabeçalho utilizando ficheiros de configuração ou programaticamente. 

Também pode controlar as definições de cache do portal do Azure através da definição [regras de colocação em cache de CDN](cdn-caching-rules.md). Se criar um ou mais de colocação em cache as regras e definir seu comportamento de colocação em cache **substituir** ou **ignorar a cache**, as definições de colocação em cache fornecido de origem discutidas neste artigo são ignoradas. Para obter informações sobre os conceitos gerais de colocação em cache, consulte [funciona como o cache](cdn-how-caching-works.md).

> [!TIP]
> Pode optar por não definir nenhum valor de TTL num ficheiro. Neste caso, o CDN do Azure aplica automaticamente um TTL predefinido de sete dias, a menos que tiver configurado a colocação em cache as regras no portal do Azure. Esta predefinição TTL só se aplica a otimizações de entrega geral web. Para otimizações de ficheiros grandes, o TTL predefinido é um dia e, para suporte de dados de transmissão em fluxo otimizações, o TTL predefinido é de um ano.
> 
> Para obter mais informações sobre como funciona o CDN do Azure para acelerar o acesso a ficheiros e outros recursos, consulte [descrição geral da rede de entrega de conteúdos de Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Definir cabeçalhos Cache-Control ao utilizar regras de colocação em cache de CDN
O método preferencial para a definição de um servidor web `Cache-Control` cabeçalho consiste em utilizar regras de colocação em cache no portal do Azure. Para obter mais informações sobre as regras de cache de CDN, veja [comportamento de cache com regras de colocação em cache de CDN do Azure de controle](cdn-caching-rules.md).

> [!NOTE] 
> Estão disponíveis apenas para regras de colocação em cache **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis. Para **CDN do Azure Premium da Verizon** perfis, tem de utilizar o [motor de regras de CDN do Azure](cdn-rules-engine.md) no **gerir** portal para uma funcionalidade semelhante.

**Para navegar para a página de regras de colocação em cache de CDN**:

1. No portal do Azure, selecione um perfil da CDN, em seguida, selecione o ponto final para o servidor web.

1. No painel esquerdo, em Definições, selecione **Regras de colocação em cache**.

   ![Botão de regras de colocação em cache da CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   É apresentada a página **Regras de colocação em cache**.

   ![Página de colocação em cache de CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Para definir uma web cabeçalhos Cache-Control do servidor ao utilizar regras de colocação em cache global:**

1. Sob **Global, as regras de cache**, defina **comportamento de colocação em cache de cadeia de consulta** para **ignorar cadeias de consulta** e defina **comportamento de colocação em cache** para  **Substituir**.
      
1. Para **duração de expiração da Cache**, introduza 3600 no **segundos** caixa ou 1 no **horas** caixa. 

   ![Exemplo de regras de colocação em cache global de CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Esta regra de colocação em cache global define uma duração de cache de uma hora e afeta todos os pedidos para o ponto final. Substitui qualquer `Cache-Control` ou `Expires` cabeçalhos HTTP que são enviados pelo servidor de origem especificado pelo ponto final.   

1. Selecione **Guardar**.

**Para definir cabeçalhos de Cache-Control do ficheiro de um servidor web utilizando regras de colocação em cache personalizadas:**

1. Sob **personalizada, regras de colocação em cache**, criar duas condições de correspondência:

     a. Para a primeira condição de correspondência, defina **corresponde à condição** ao **caminho** e introduza `/webfolder1/*` para **corresponde ao valor**. Definir **comportamento de colocação em cache** ao **substituir** e introduza 4 no **horas** caixa.

     b. Para a segunda condição de correspondência, defina **corresponde à condição** ao **caminho** e introduza `/webfolder1/file1.txt` para **corresponde ao valor**. Definir **comportamento de colocação em cache** ao **substituir** e introduzir 2 no **horas** caixa.

    ![Exemplo de regras de colocação em cache personalizado de CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    A primeira regra de colocação em cache personalizada define uma duração de cache de quatro horas para todos os ficheiros no `/webfolder1` pasta no servidor de origem especificado pelo seu ponto final. A segunda regra substitui a primeira regra para o `file1.txt` apenas de ficheiros e define uma duração de cache de duas horas para o mesmo.

1. Selecione **Guardar**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>A definição de cabeçalhos Cache-Control utilizando ficheiros de configuração
Para conteúdo estático, como imagens e folhas de estilo, pode controlar a frequência de atualização ao modificar os **applicationHost. config** ou **Web. config** ficheiros de configuração para a sua aplicação web. Para definir o `Cache-Control` cabeçalho para o seu conteúdo, utilize o `<system.webServer>/<staticContent>/<clientCache>` elemento em qualquer um dos ficheiros.

### <a name="using-applicationhostconfig-files"></a>Usando arquivos de applicationHost. config
O **applicationHost. config** ficheiro é o arquivo de raiz do sistema de configuração do IIS. As definições de configuração num **applicationHost. config** ficheiro afetam todos os aplicativos do site, mas são substituídas por definições de qualquer **Web. config** arquivos existentes de um aplicativo web.

### <a name="using-webconfig-files"></a>Usando arquivos Web. config
Com um **Web. config** ficheiro, pode personalizar a forma como seu aplicativo de toda a web ou um diretório específico na sua aplicação web se comporta. Normalmente, tem, pelo menos, um **Web. config** ficheiro na pasta raiz da sua aplicação web. Para cada **Web. config** ficheiro numa pasta específica, as definições de configuração afetam a tudo o que de nessa pasta e suas subpastas, a menos que eles são substituídos no nível de subpasta por outro **Web. config** ficheiro. 

Por exemplo, pode definir uma `<clientCache>` elemento numa **Web. config** ficheiro na pasta raiz da sua aplicação web para colocar em cache todo o conteúdo estático em seu aplicativo web por três dias. Também pode adicionar um **Web. config** ficheiro numa subpasta com o conteúdo mais variável (por exemplo, `\frequent`) e defina seu `<clientCache>` elemento em cache o conteúdo a subpasta durante seis horas. O resultado líquido é que os conteúdos em todo o site da web é colocado em cache por três dias, exceto para qualquer conteúdo a `\frequent` diretório, que é colocado em cache por apenas seis horas.  

O exemplo de ficheiro de configuração XML seguinte mostra como definir o `<clientCache>` elemento para especificar uma duração máxima de três dias:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Para utilizar o **cacheControlMaxAge** atributo, tem de definir o valor da **cacheControlMode** atributo para `UseMaxAge`. Esta definição causou o cabeçalho de HTTP e a diretiva, `Cache-Control: max-age=<nnn>`, para ser adicionado à resposta. O formato do valor de intervalo de tempo para o **cacheControlMaxAge** atributo é `<days>.<hours>:<min>:<sec>`. Seu valor é convertido em segundos e é utilizado como o valor do `Cache-Control` `max-age` diretiva. Para obter mais informações sobre o `<clientCache>` elemento, consulte [Cache do cliente <clientCache> ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Definir cabeçalhos Cache-Control através de programação
Para aplicativos ASP.NET, controla a CDN comportamento de cache por meio de programação, definindo a **HttpResponse.Cache** propriedade da .NET API. Para obter informações sobre o **HttpResponse.Cache** propriedade, veja [propriedade HttpResponse.Cache](https://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) e [HttpCachePolicy classe](https://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Para por meio de programação aplicação conteúdo em cache no ASP.NET, siga estes passos:
   1. Certifique-se de que o conteúdo está marcado como em cache através da definição `HttpCacheability` para `Public`. 
   1. Definir um validador de cache ao chamar um dos seguintes `HttpCachePolicy` métodos:
      - Chamar `SetLastModified` para definir um valor de timestamp para o `Last-Modified` cabeçalho.
      - Chamar `SetETag` para definir um valor para o `ETag` cabeçalho.
   1. Opcionalmente, especifique uma hora de expiração do cache chamando `SetExpires` para definir um valor para o `Expires` cabeçalho. Caso contrário, a heurística de cache padrão descrita anteriormente neste documento aplicam-se.

Por exemplo, a cache de conteúdo de uma hora, adicione o seguinte código do c#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Teste o cabeçalho Cache-Control
Pode verificar facilmente as definições de TTL de seu conteúdo da web. Com o seu browser [ferramentas de programação](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), teste, que inclui o seu conteúdo da web a `Cache-Control` cabeçalho de resposta. Também pode utilizar uma ferramenta como **wget**, [Postman](https://www.getpostman.com/), ou [Fiddler](https://www.telerik.com/fiddler) para examinar os cabeçalhos de resposta.

## <a name="next-steps"></a>Próximos Passos
* [Leia os detalhes sobre o **clientCache** elemento](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Leia a documentação para o **HttpResponse.Cache** propriedade](https://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Leia a documentação para o **HttpCachePolicy classe**](https://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Saiba mais sobre conceitos de colocação em cache](cdn-how-caching-works.md)

---
title: Resolver problemas de análise no Application Insights do Azure | Documentos da Microsoft
description: 'Problemas com a análise do Application Insights? Comece aqui. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: mbullwin
ms.openlocfilehash: ecf0638aa999208331603ac30ccf4eb17b3c4500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692385"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Resolver problemas de Análise no Application Insights
Problemas com [Analytics do Application Insights](analytics.md)? Comece aqui. A análise é a ferramenta de pesquisa poderosa do Azure Application Insights.

## <a name="limits"></a>Limits
* No momento, os resultados das consultas estão limitados a apenas durante uma semana de dados nos últimos.
* Browsers Testamos em: edições mais recente do Chrome, o Microsoft Edge e o Internet Explorer.

## <a name="known-incompatible-browser-extensions"></a>Extensões de browser incompatível conhecidos
* Ghostery

Desativar a extensão ou utilize um browser diferente.

## <a name="e-a"></a> "Unexpected error"
![Ecrã de erro inesperado](media/analytics-troubleshooting/010.png)

Ocorreu um erro interno durante a exceção de tempo de execução de portal sem tratamento.

* Limpe a cache do navegador.

## <a name="e-b"></a>403... Volte a tentar recarregar
![403... Volte a tentar recarregar](media/analytics-troubleshooting/020.png)

Ocorreu um erro relacionado com a autenticação (durante a autenticação ou durante a geração do token de acesso). O portal pode não ter nenhuma forma de recuperar sem alterar as definições do browser.

* Certifique-se [cookies de terceiros estão ativados](#cookies) no browser. 

## <a name="authentication"></a>403... Certifique-se a zona de segurança
![403... .verify zona de segurança](media/analytics-troubleshooting/030.png)

Ocorreu um erro relacionado com a autenticação (durante a autenticação ou durante a geração do token de acesso). O portal pode não ter nenhuma forma de recuperar sem alterar as definições do browser.

1. Certifique-se [cookies de terceiros estão ativados](#cookies) no browser. 
2. Utilizou um favorito, marcador ou ligação guardada para abrir o portal do Analytics? Iniciou sessão com credenciais diferentes das que utilizou quando guardou a ligação?
3. Tente utilizar uma janela do browser em privado/incognito (depois de fechar todas essas janelas). Terá de fornecer as suas credenciais. 
4. Abrir outra janela do browser (normal) e aceda a [Azure](https://portal.azure.com). Termine a sessão. Em seguida, abra a ligação e inicie a sessão com as credenciais corretas.
5. Os utilizadores do Microsoft Edge e Internet Explorer também podem obter este erro quando as definições de zona de confiança não são suportadas.
   
    Verifique se ambos [portal do Analytics](https://portal.azure.com) e [portal do Azure Active Directory](https://portal.azure.com) estão na mesma zona de segurança:
   
   * No Internet Explorer, abra **opções da Internet**, **Security**, **sites fidedignos**, **Sites**:
     
     ![Caixa de diálogo Opções da Internet, a adição de um site aos Sites fidedignos](media/analytics-troubleshooting/033.png)
     
     Na lista de Sites, se algum dos URLs seguintes estiverem incluídos, certifique-se de que também são incluídos outros:
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... Recurso não encontrado
![404... não foram encontrados recursos](media/analytics-troubleshooting/040.png)

Recurso de aplicação foi eliminado do Application Insights e não deixa de poder está disponível. Isto pode acontecer se tiver guardado o URL para a página de análise.

## <a name="e-e"></a>403 ... Sem autorização
![403... não autorizado](media/analytics-troubleshooting/050.png)

Não tem permissão para abrir esta aplicação no Analytics.

* Recebeu a ligação de outra pessoa? Peça-lhe para certificar-se de que se encontra o [leitores ou contribuidores para este grupo de recursos](../../azure-monitor/app/resources-roles-access-control.md).
* Tiver guardado o link com credenciais diferentes? Abra o [portal do Azure](https://portal.azure.com), termine sessão e, em seguida, tente esta ligação novamente, fornecendo as credenciais corretas.

## <a name="html-storage"></a>403 ... Armazenamento HTML5
Nosso portal utiliza HTML5 localStorage e sessionStorage.

* Chrome: As definições, privacidade, definições de conteúdo.
* Internet Explorer: Opções da Internet, separador Avançadas, segurança, ativar o armazenamento de DOM

![403... tente ativar armazenamento HTML5](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... Subscrição não encontrada
![404 ... Subscrição não encontrada](media/analytics-troubleshooting/070.png)

O URL é inválido. 

* Abra o recurso de aplicação no [portal do Application Insights](https://portal.azure.com). Em seguida, utilize o botão de análise.

## <a name="e-h"></a>... 404 página não existe
![404 ... Página não existe](media/analytics-troubleshooting/080.png)

O URL é inválido.

* Abra o recurso de aplicação no [portal do Application Insights](https://portal.azure.com). Em seguida, utilize o botão de análise.

## <a name="cookies"></a>Permitir cookies de terceiros
  Ver [como desativar os cookies de terceiros](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), mas observe que precisamos **ativar** -los.


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]


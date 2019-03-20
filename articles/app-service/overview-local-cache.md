---
title: Descrição geral de local cache - serviço de aplicações do Azure | Documentos da Microsoft
description: Este artigo descreve como ativar, redimensionar e consultar o estado da funcionalidade de Cache Local do serviço de aplicações do Azure
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1d6e233509b50f0b03678f2e62267169d02133a1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990773"
---
# <a name="azure-app-service-local-cache-overview"></a>Descrição geral de Cache Local do serviço de aplicações do Azure

> [!NOTE]
> Local cache não é suportada em aplicações de função ou em aplicações de serviço de aplicações em contentores, tal como no [serviço de aplicações no Linux](containers/app-service-linux-intro.md).


Conteúdo de serviço de aplicações do Azure é armazenado no armazenamento do Azure e é exibido a cópia de segurança de forma durável, como uma partilha de conteúdo. Este design concebida para funcionar com uma variedade de aplicações e tem os seguintes atributos:  

* O conteúdo é compartilhado entre várias instâncias de máquina virtual (VM) da aplicação.
* O conteúdo é durável e pode ser modificado através da execução de aplicações.
* Ficheiros de registo e ficheiros de dados de diagnóstico estão disponíveis na mesma pasta conteúda partilhada.
* Publicar o novo conteúdo diretamente atualiza a pasta de conteúdo. Pode ver imediatamente o mesmo conteúdo através do site do SCM e a execução aplicação (normalmente, algumas tecnologias como ASP.NET iniciar reiniciar a aplicação em algumas alterações de ficheiro para obter o conteúdo mais recente).

Embora muitas aplicações utilizam uma ou todas estas funcionalidades, algumas aplicações precisam apenas de um arquivo de conteúdo de alto desempenho, só de leitura que eles possam executar com elevada disponibilidade. Estas aplicações podem beneficiar de uma instância de VM de uma cache local específica.

A funcionalidade de Cache Local do serviço de aplicações do Azure fornece uma vista de função da web do seu conteúdo. Este conteúdo é uma cache de write-mas-rejeição do conteúdo do armazenamento que é criada a inicialização de forma assíncrona no local. Quando a cache estiver pronta, o site é mudado para executar o conteúdo em cache. As aplicações que são executadas no Local Cache têm as seguintes vantagens:

* Eles estão imunes a latências que ocorrem quando acede a conteúdo no armazenamento do Azure.
* Eles estão imunes para as atualizações planeadas ou tempos de inatividade não planeados e outras perturbações com armazenamento do Azure que ocorrem nos servidores que atendem a partilha de conteúdo.
* Eles têm menos reinicializações de aplicação devido a alterações de partilha de armazenamento.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Como a cache local altera o comportamento do serviço de aplicações
* _D:\home_ aponta para a cache local, o que é criada na instância de VM quando a aplicação é iniciada. _D:\Local_ continua apontar para o armazenamento temporário de VM específicos.
* A cache local contém uma cópia única dos _/site_ e _/siteextensions_ pastas de arquivo de conteúdo partilhado, em _D:\home\site_ e _D:\home\ siteextensions_, respectivamente. Os ficheiros são copiados para a cache local quando a aplicação é iniciada. O tamanho das duas pastas para cada aplicação é limitado para 300 MB por predefinição, mas pode aumentá-la até 2 GB.
* A cache local é de leitura / escrita. No entanto, qualquer modificação é eliminada quando a aplicação mova as máquinas virtuais ou é reiniciada. Não utilize a cache local para aplicações que armazenam dados de missão crítica no arquivo de conteúdo.
* _D:\home\LogFiles_ e _D:\home\Data_ conter ficheiros de registo e dados da aplicação. As duas subpastas são armazenadas localmente na instância de VM e são copiadas para o arquivo de conteúdo partilhado periodicamente. Aplicações podem manter ficheiros de registo e os dados, ao escrevê-las a essas pastas. No entanto, a cópia para o arquivo de conteúdo partilhado é melhor esforço, portanto, é possível que os ficheiros de registo de dados seja perdida devido a uma falha repentina de uma instância VM.
* [A transmissão de registos](troubleshoot-diagnostic-logs.md#streamlogs) é afetada pela cópia de melhor esforço. Pode observar que até um atraso de um minuto nos registos de transmissão em fluxo contínuo.
* O arquivo de conteúdo partilhado, existe uma alteração na estrutura da pasta do _LogFiles_ e _dados_ pastas para aplicações que utilizam a cache local. Existem agora subpastas-o de que seguem o padrão de nomenclatura de "Identificador exclusivo" + o carimbo de data / hora. Cada um das subpastas corresponde a uma instância de VM em que a aplicação está em execução ou foi executada.
* Outras pastas nas _D:\home_ permanecem na local cache e não são copiados para o arquivo de conteúdo partilhado.
* Implementação de aplicações através de qualquer método suportado diretamente publica o arquivo de conteúdo partilhado durável. Para atualizar o _D:\home\site_ e _D:\home\siteextensions_ pastas no local cache, a aplicação tem de ser reiniciado. Para tornar o ciclo de vida totalmente integrada, veja as informações neste artigo.
* A exibição de conteúdo padrão do SCM site continua a ser que o arquivo de conteúdo partilhado.

## <a name="enable-local-cache-in-app-service"></a>Ativar a Cache Local no serviço de aplicações
Configurar o Local Cache ao utilizar uma combinação de definições da aplicação reservado. Pode configurar estas definições de aplicação através dos seguintes métodos:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurar a Local Cache com o portal do Azure
<a name="Configure-Local-Cache-Portal"></a>

Ativar a Local Cache numa base por-web-app ao utilizar esta definição de aplicação: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Definições de aplicação do portal do Azure: Cache Local](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurar a Local Cache ao utilizar o Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Alterar a definição de tamanho na Local Cache
Por predefinição, o tamanho do local cache for **300 MB**. Isto inclui o /site e /siteextensions pastas que são copiadas do arquivo de conteúdo, bem como quaisquer pastas de registos e de dados criadas localmente. Para aumentar este limite, utilize a definição de aplicação `WEBSITE_LOCAL_CACHE_SIZEINMB`. Pode aumentar o tamanho até **2GB** (2000 MB) por aplicação.

## <a name="best-practices-for-using-app-service-local-cache"></a>Melhores práticas para utilizar a Cache Local do serviço de aplicações
Recomendamos que utilize o Local Cache em conjunto com o [ambientes de teste](../app-service/deploy-staging-slots.md) funcionalidade.

* Adicionar a *adesivo* definição de aplicação `WEBSITE_LOCAL_CACHE_OPTION` com o valor `Always` para sua **produção** ranhura. Se estiver a utilizar `WEBSITE_LOCAL_CACHE_SIZEINMB`, também adicioná-lo como uma definição de auto-adesiva para seu bloco de produção.
* Criar uma **teste** espaço e publicar no seu bloco de teste. Geralmente não definem o bloco de teste para utilizar a Local Cache para permitir que um ciclo de vida de compilação, implantação e teste totalmente integrado para se obter os benefícios da Local Cache para o bloco de produção de teste.
* Teste seu site em relação a seu bloco de teste.  
* Quando estiver pronto, emitir uma [operação de troca](../app-service/deploy-staging-slots.md#Swap) entre seu teste e produção ranhuras.  
* Definições de auto-adesivas incluem nome e auto-adesivo para um bloco. Então, quando o bloco de teste obtém colocado em produção, ele herda as definições de aplicação do Local Cache. O bloco de produção trocado recentemente será executado em relação à cache local após alguns minutos e será ser aquecido como parte de aquecimento de ranhura depois de troca. Então, quando a troca de ranhura estiver concluída, o bloco de produção está em execução em relação à cache local.

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)
### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Como posso saber se a Local Cache aplica-se a minha aplicação?
Se a sua aplicação precisa de um arquivo de conteúdo de alto desempenho, fiável, não utiliza o arquivo de conteúdos para escrever dados críticos em tempo de execução e é inferior a 2 GB de tamanho total, em seguida, a resposta for "Sim"! Para obter o tamanho total das pastas /site e /siteextensions, pode utilizar a extensão de site "Azure Web Apps utilização do disco."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Como posso saber se meu site mudou para o uso do Local Cache?
Se estiver a utilizar a funcionalidade de Local Cache com ambientes de teste, a operação de troca não for concluída até que a Local Cache é preparada. Para verificar se o seu site for executado em relação a Local Cache, pode verificar a variável de ambiente do processo de trabalho `WEBSITE_LOCALCACHE_READY`. Utilize as instruções sobre a [variável de ambiente do processo de trabalho](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) página para acessar a variável de ambiente do processo de trabalho em várias instâncias.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Acabou de publicar novas alterações, mas meu aplicativo aparenta não tê-los. Porquê?
Se a aplicação utilizar a Local Cache, em seguida, terá de reiniciar o seu site para obter as alterações mais recentes. Não pretende publicar as alterações a um site de produção? Ver as opções de bloco na seção anterior de práticas recomendadas.

### <a name="where-are-my-logs"></a>Onde estão meus registos?
Com a Local Cache, os seus registos e as pastas de dados parecem um pouco diferentes. No entanto, a estrutura de suas subpastas permanece a mesma, exceto pelo fato das subpastas são aninhadas numa subpasta com o formato "VM Identificador exclusivo" + o carimbo de data / hora.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tenho o Cache Local ativada, mas a minha aplicação ainda é reiniciada. Por que não é? Pensei que ajudou a Local Cache com reinícios frequentes do aplicativo.
Local Cache o ajuda a impedir que os reinícios da aplicação relacionada com o armazenamento. No entanto, a aplicação foi ainda são realizadas reinicializações durante as atualizações de infraestrutura planeada da VM. Os reinícios de aplicação geral que ocorrer com a Cache Local ativado devem ser menos.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Local Cache excluir todos os diretórios de ser copiado para a unidade local mais depressa?
Como parte do passo que copia o conteúdo de armazenamento, qualquer uma das pastas com o nome repositório está excluída. Isto ajuda a com cenários em que o conteúdo do site pode conter um repositório de controle de código-fonte que pode não ser necessária no dia a dia operação da aplicação. 

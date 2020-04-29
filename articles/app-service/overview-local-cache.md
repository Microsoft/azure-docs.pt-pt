---
title: Cache local
description: Saiba como funciona o cache local no Azure App Service e como ativar, redimensionar e consultar o estado da cache local da sua aplicação.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 1945730acaddb0c1c7ee1b28eeb926635efad643
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78227886"
---
# <a name="azure-app-service-local-cache-overview"></a>Visão geral do Cache Local do Serviço de Aplicações Azure

> [!NOTE]
> A cache local não é suportada em aplicações de função ou aplicações de Serviço de Aplicações [contentorizadas,](app-service-web-get-started-windows-container.md) como em Recipientes Windows ou no [Serviço de Aplicações no Linux](containers/app-service-linux-intro.md).


O conteúdo do Serviço de Aplicações Azure é armazenado no Armazenamento Azure e é surgido de forma duradoura como partilha de conteúdo. Este design destina-se a trabalhar com uma variedade de apps e tem os seguintes atributos:  

* O conteúdo é partilhado em várias instâncias de máquina virtual (VM) da aplicação.
* O conteúdo é durável e pode ser modificado através da execução de apps.
* Os ficheiros de registo e os ficheiros de dados de diagnóstico estão disponíveis na mesma pasta de conteúdo partilhado.
* A publicação de novos conteúdos atualiza diretamente a pasta de conteúdos. Pode visualizar imediatamente o mesmo conteúdo através do website SCM e da aplicação de execução (normalmente algumas tecnologias como ASP.NET iniciar um reinício de aplicação em algumas alterações de ficheiros para obter os conteúdos mais recentes).

Apesar de muitas aplicações utilizarem uma ou todas estas funcionalidades, algumas aplicações apenas precisam de uma loja de conteúdos de alto desempenho, apenas de leitura, da qual podem funcionar com elevada disponibilidade. Estas aplicações podem beneficiar de uma instância VM de uma cache local específica.

A funcionalidade Cache Local do Serviço de Aplicações Azure proporciona uma visão de papel web dos seus conteúdos. Este conteúdo é uma cache de escrita mas descartar o seu conteúdo de armazenamento que é criado assincronicamente no local. Quando a cache estiver pronta, o site é trocado para ser executado contra o conteúdo em cache. As aplicações que funcionam em Cache Local têm os seguintes benefícios:

* São imunes às tardios que ocorrem quando acedem ao conteúdo no Armazenamento Azure.
* São imunes às atualizações planeadas ou aos tempos de paragem não planeados e a quaisquer outras perturbações com o Armazenamento Azure que ocorram em servidores que servem a partilha de conteúdos.
* Têm menos reinícios de aplicações devido a alterações nas ações de armazenamento.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Como a cache local muda o comportamento do Serviço de Aplicações
* _D:\home_ aponta para a cache local, que é criada na instância VM quando a aplicação começa. _D:\local_ continua a apontar para o armazenamento temporário em VM-specific.
* A cache local contém uma cópia única das pastas _/site_ _e/extensões_ do site da loja de conteúdos partilhados, em _D:\home\site_ e _extensões de site D:\home\,_ respectivamente. Os ficheiros são copiados para a cache local quando a aplicação começa. O tamanho das duas pastas para cada aplicação está limitado a 300 MB por padrão, mas pode aumentá-lo até 2 GB. Se os ficheiros copiados excederem o tamanho da cache local, o Serviço de Aplicações ignora silenciosamente a cache local e lê-se a partir da partilha remota de ficheiros.
* A cache local é leitura-escrita. No entanto, qualquer modificação é descartada quando a aplicação move máquinas virtuais ou é reiniciada. Não utilize o cache local para apps que armazenem dados críticos de missão na loja de conteúdos.
* _D:\home\LogFiles_ e _D:\home\Dados_ contêm ficheiros de registo e dados de aplicações. As duas subpastas são armazenadas localmente na instância VM, e são copiadas periodicamente para a loja de conteúdos partilhados. As aplicações podem persistir ficheiros de registo e dados escrevendo-os para estas pastas. No entanto, a cópia para a loja de conteúdos partilhados é o melhor esforço, pelo que é possível que os ficheiros de registo e os dados sejam perdidos devido a uma queda repentina de uma instância VM.
* [O streaming de registo](troubleshoot-diagnostic-logs.md#stream-logs) supor-se na cópia do melhor esforço. Pode observar até um minuto de atraso nos registos transmitidos.
* Na loja de conteúdos partilhados, existe uma alteração na estrutura da pasta das pastas _LogFiles_ e _Data_ para aplicações que utilizam o cache local. Existem agora subpastas nelas que seguem o padrão de nomeação de "identificador único" + carimbo de tempo. Cada uma das subpastas corresponde a um caso VM em que a aplicação está em execução ou está a funcionar.
* Outras pastas em _D:\home_ permanecem na cache local e não são copiadas para a loja de conteúdos partilhados.
* A implementação da aplicação através de qualquer método suportado publica diretamente na loja de conteúdos partilhados duráveis. Para refrescar as pastas _d:\home\site_ e _d:\home\siteextensions_ na cache local, a aplicação precisa de ser reiniciada. Para tornar o ciclo de vida perfeito, consulte a informação mais tarde neste artigo.
* A visão predefinida do conteúdo do site SCM continua a ser a da loja de conteúdos partilhados.

## <a name="enable-local-cache-in-app-service"></a>Ativar cache local no serviço de aplicações
Configura o Cache Local utilizando uma combinação de definições de aplicações reservadas. Pode configurar estas definições de aplicações utilizando os seguintes métodos:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configure cache local utilizando o portal Azure
<a name="Configure-Local-Cache-Portal"></a>

Você ativa o Cache Local numa base por web-app usando esta definição de aplicação:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Definições de aplicativos do portal Azure: Cache Local](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configure a cache local utilizando o Gestor de Recursos Azure
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

## <a name="change-the-size-setting-in-local-cache"></a>Alterar a definição de tamanho em Cache Local
Por padrão, o tamanho da cache local é **de 300 MB**. Isto inclui as pastas /site e/siteextensions que são copiadas da loja de conteúdos, bem como quaisquer registos e pastas de dados criadas localmente. Para aumentar este limite, `WEBSITE_LOCAL_CACHE_SIZEINMB`utilize a definição da aplicação . Pode aumentar o tamanho até **2 GB** (2000 MB) por app.

## <a name="best-practices-for-using-app-service-local-cache"></a>Boas práticas para usar o Cache Local do Serviço de Aplicações
Recomendamos que utilize cache local em conjunto com a funcionalidade [Ambientes de Encenação.](../app-service/deploy-staging-slots.md)

* Adicione a `WEBSITE_LOCAL_CACHE_OPTION` definição de `Always` aplicação *pegajosa* com o valor à sua ranhura **de Produção.** Se estiver a `WEBSITE_LOCAL_CACHE_SIZEINMB`utilizar, adicione-o também como uma definição pegajosa à sua ranhura de Produção.
* Crie uma ranhura **de Encenação** e publique na sua slot de encenação. Normalmente, não se define a ranhura de preparação para utilizar a Cache Local para permitir um ciclo de vida de teste de implementação de sem emenda para a realização se obtém os benefícios da Cache Local para a ranhura de produção.
* Teste o seu site contra a sua ranhura de encenação.  
* Quando estiver pronto, emita uma operação de [troca](../app-service/deploy-staging-slots.md#Swap) entre as suas ranhuras de Preparação e Produção.  
* As configurações pegajosas incluem nome e pegajosa para uma ranhura. Assim, quando a ranhura de Encenação é trocada em Produção, herda as definições da aplicação Cache Local. A ranhura de produção recém-trocada irá contra a cache local após alguns minutos e será aquecida como parte do aquecimento da ranhura após a troca. Assim, quando a troca de slot estiver completa, a sua ranhura de produção está a correr contra a cache local.

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Como posso saber se o Cache Local se aplica à minha aplicação?
Se a sua aplicação necessitar de uma loja de conteúdos de alto desempenho e fiável, não utiliza a loja de conteúdos para escrever dados críticos no prazo de execução, e tem menos de 2 GB em tamanho total, então a resposta é "sim"! Para obter o tamanho total das suas pastas/site e /extensões/site, pode utilizar a extensão do site "Utilização do Disco de Web Apps Azure".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Como posso saber se o meu site mudou para usar o Cache Local?
Se estiver a utilizar a função Cache Local com Ambientes de Preparação, a operação de troca não termina até que a Cache Local esteja aquecida. Para verificar se o seu site está a correr contra `WEBSITE_LOCALCACHE_READY`a Cache Local, pode verificar a variável ambiente do processo do trabalhador . Utilize as instruções na página [variável](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) ambiente do processo do trabalhador para aceder à variável ambiente do processo do trabalhador em várias instâncias.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Acabei de publicar novas mudanças, mas a minha aplicação não parece tê-las. Porquê?
Se a sua aplicação utilizar cache local, então precisa reiniciar o seu site para obter as mais recentes alterações. Não quer publicar alterações num site de produção? Consulte as opções de slot na secção de boas práticas anteriores.

### <a name="where-are-my-logs"></a>Onde estão os meus registos?
Com a Cache Local, os seus registos e pastas de dados parecem um pouco diferentes. No entanto, a estrutura das suas subpastas permanece a mesma, exceto que as subpastas estão aninhadas sob uma subpasta com o formato "identificador VM único" + carimbo de tempo.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tenho cache local ativado, mas a minha aplicação ainda é reiniciada. Porquê? Pensei que o Cache Local ajudasse com o reinício frequente das aplicações.
A Cache local ajuda a evitar o reinício da aplicação relacionada com o armazenamento. No entanto, a sua aplicação ainda poderá ser reiniciada durante as atualizações planeadas da infraestrutura do VM. A aplicação geral reinicia que experimenta com a Cache Local ativada deve ser menor.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>A Cache Local exclui que os diretórios sejam copiados para a unidade local mais rápida?
Como parte do passo que copia o conteúdo de armazenamento, qualquer pasta que seja chamada repositório é excluída. Isto ajuda com cenários em que o conteúdo do seu site pode conter um repositório de controlo de fonte que pode não ser necessário no funcionamento diário da app. 

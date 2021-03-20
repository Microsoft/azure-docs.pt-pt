---
title: Cache local
description: Saiba como funciona a cache local no Azure App Service e como ativar, redimensionar e consultar o estado da cache local da sua aplicação.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 81782f63199a9fe8f43f56aeefcd1c68951d57a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852257"
---
# <a name="azure-app-service-local-cache-overview"></a>Visão geral da Cache local do Serviço de Aplicações Azure

> [!NOTE]
> A cache local não é suportada em aplicações de funções ou aplicações de Serviço de Aplicações [contentorizadas,](quickstart-custom-container.md?pivots=container-windows) tais como em Contentores windows ou no [Serviço de Aplicações no Linux.](overview.md#app-service-on-linux)


O conteúdo do Azure App Service é armazenado no Azure Storage e é surgido de forma duradoura como uma partilha de conteúdo. Este design destina-se a trabalhar com uma variedade de aplicações e tem os seguintes atributos:  

* O conteúdo é partilhado em várias instâncias da máquina virtual (VM) da aplicação.
* O conteúdo é durável e pode ser modificado através da execução de aplicações.
* Os ficheiros de registo e os ficheiros de dados de diagnóstico estão disponíveis na mesma pasta de conteúdo partilhado.
* A publicação de novos conteúdos atualiza diretamente a pasta de conteúdos. Pode ver imediatamente o mesmo conteúdo através do website SCM e da aplicação de execução (normalmente algumas tecnologias como ASP.NET iniciar um reinício de aplicações em algumas alterações de ficheiros para obter o conteúdo mais recente).

Apesar de muitas aplicações utilizarem uma ou todas estas funcionalidades, algumas aplicações apenas precisam de uma loja de conteúdo de alto desempenho e apenas de leitura, que podem executar com alta disponibilidade. Estas aplicações podem beneficiar de uma instância VM de uma cache local específica.

A funcionalidade Cache Local do Serviço de Aplicações Azure proporciona uma visão web do seu conteúdo. Este conteúdo é uma cache de write-but-discard do seu conteúdo de armazenamento que é criado assíncronamente no local de arranque. Quando a cache estiver pronta, o site é comutado para funcionar contra o conteúdo em cache. As aplicações que funcionam na Cache Local têm os seguintes benefícios:

* São imunes a latências que ocorrem quando acedem ao conteúdo no Azure Storage.
* São imunes às atualizações planeadas ou aos tempos de inatividade não planeados e quaisquer outras perturbações com o Azure Storage que ocorram nos servidores que servem a partilha de conteúdos.
* Têm menos recomeçamento de apps devido a alterações nas partilhas de armazenamento.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Como a cache local muda o comportamento do Serviço de Aplicações
* _D:\home_ aponta para a cache local, que é criada na instância VM quando a aplicação começa. _D:\local_ continua a apontar para o armazenamento temporário específico de VM.
* A cache local contém uma cópia única das pastas _/site_ _e/siteextensions_ da loja de conteúdos partilhados, em _D:\home\site e_ _D:\home\siteextensions,_ respectivamente. Os ficheiros são copiados para a cache local quando a aplicação começa. O tamanho das duas pastas para cada aplicação está limitado a 1 GB por padrão, mas pode ser aumentado para 2 GB. Note que à medida que o tamanho da cache aumenta, levará mais tempo para carregar a cache. Se aumentou o limite de cache local para 2 GB e os ficheiros copiados excedem o tamanho máximo de 2 GB, o Serviço de Aplicações ignora silenciosamente a cache local e lê a partir da partilha remota de ficheiros. Se não houver um limite definido ou o limite for definido para algo inferior a 2 GB e os ficheiros copiados excederem o limite, a implementação ou troca pode falhar com um erro.
* A cache local é ler-escrever. No entanto, qualquer modificação é descartada quando a aplicação move máquinas virtuais ou é reiniciada. Não utilize a cache local para aplicações que armazenam dados críticos da missão na loja de conteúdos.
* _D:\home\LogFiles_ e _D:\home\Os dados_ contêm ficheiros de registo e dados de aplicações. As duas subpas são armazenadas localmente no caso VM e são copiadas periodicamente para a loja de conteúdos partilhados. As aplicações podem persistir em registar ficheiros e dados escrevendo-os para estas pastas. No entanto, a cópia para a loja de conteúdos partilhados é o melhor esforço, pelo que é possível que os ficheiros de registo e dados sejam perdidos devido a uma súbita queda de uma instância VM.
* [O streaming de registo](troubleshoot-diagnostic-logs.md#stream-logs) é afetado pela cópia de melhor esforço. Pode observar até um minuto de atraso nos registos transmitidos.
* Na loja de conteúdos partilhados, há uma alteração na estrutura de pastas dos _Ficheiros de Log E_ Das pastas de _Dados_ para aplicações que utilizam a cache local. Existem agora sub-14 que seguem o padrão de nomeação de "identificador único" + carimbo temporal. Cada uma das subpastas corresponde a uma instância VM em que a aplicação está a funcionar ou foi executada.
* Outras pastas em _D:\home_ permanecem na cache local e não são copiadas para a loja de conteúdos partilhados.
* A implementação da aplicação através de qualquer método suportado publica diretamente na loja de conteúdos partilhados durável. Para refrescar as pastas _D:\home\home\siteextensions_ na cache local, a aplicação precisa de ser reiniciada.  Para tornar o ciclo de vida perfeito, consulte a informação mais tarde neste artigo.
* A visão de conteúdo padrão do site SCM continua a ser a da loja de conteúdos partilhados.

## <a name="enable-local-cache-in-app-service"></a>Ativar cache local no serviço de aplicações 

> [!NOTE]
> A Cache local não é suportada no nível **F1** ou **D1.** 

Configura a Cache Local utilizando uma combinação de configurações de aplicações reservadas. Pode configurar estas definições de aplicações utilizando os seguintes métodos:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configure cache local usando o portal Azure
<a name="Configure-Local-Cache-Portal"></a>

Você ativa a Cache Local numa base de aplicação por web-app usando esta definição de aplicação: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Definições de aplicativo do portal Azure: Cache local](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configure cache local usando O Gestor de Recursos Azure
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
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Alterar a definição de tamanho na Cache Local
Por predefinição, o tamanho da cache local é **de 1 GB**. Isto inclui as pastas de /site e /siteextensions que são copiadas da loja de conteúdos, bem como quaisquer registos e pastas de dados criados localmente. Para aumentar este limite, utilize a definição da aplicação `WEBSITE_LOCAL_CACHE_SIZEINMB` . Pode aumentar o tamanho até **2 GB** (2000 MB) por aplicação. Note que levará mais tempo a carregar cache local à medida que o tamanho aumenta.

## <a name="best-practices-for-using-app-service-local-cache"></a>Melhores práticas para usar o Serviço de Aplicações Cache Local
Recomendamos que utilize cache local em conjunto com a [função Ambientes de Preparação.](../app-service/deploy-staging-slots.md)

* Adicione a definição de aplicação *pegajosa* `WEBSITE_LOCAL_CACHE_OPTION` com o valor da sua ranhura de `Always` **Produção.** Se estiver a `WEBSITE_LOCAL_CACHE_SIZEINMB` utilizar, adicione-o também como uma definição pegajosa à sua ranhura de Produção.
* Crie uma ranhura **de encenação** e publique na sua ranhura de encenação. Normalmente, não define a ranhura de preparação para usar a Cache Local para permitir um ciclo de vida de teste de construção sem costura para a realização se obter os benefícios da Cache Local para a ranhura de produção.
* Teste o seu site contra a sua ranhura de encenação.  
* Quando estiver pronto, emita uma [operação de troca](../app-service/deploy-staging-slots.md#Swap) entre as ranhuras de Encenação e Produção.  
* As configurações pegajosas incluem nome e adesivo a uma ranhura. Assim, quando a ranhura de encenação é trocada em Produção, herda as definições da aplicação Cache Local. A slot de produção recentemente trocada irá funcionar contra a cache local após alguns minutos e será aquecida como parte do aquecimento da ranhura após a troca. Assim, quando a troca de slots estiver completa, a sua ranhura de produção está a correr contra a cache local.

## <a name="frequently-asked-questions-faq"></a>Perguntas Mais Frequentes (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Como posso saber se a Cache Local se aplica à minha aplicação?
Se a sua aplicação precisa de uma loja de conteúdo de alto desempenho e fiável, não utiliza a loja de conteúdos para escrever dados críticos em tempo de execução, e tem menos de 2 GB de tamanho total, então a resposta é "sim"! Para obter o tamanho total das pastas de extensões de sites e /site, pode utilizar a extensão do site "Azure Web Apps Disk Usage".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Como posso saber se o meu site mudou para a cache local?
Se estiver a utilizar a funcionalidade Cache Local com Ambientes de Preparação, a operação de troca não se completa até que a Cache Local seja aquecida. Para verificar se o seu site está a funcionar contra a Cache Local, pode verificar a variável ambiente do processo do `WEBSITE_LOCALCACHE_READY` trabalhador. Utilize as instruções na página [variável do ambiente](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) do processo do trabalhador para aceder à variável ambiente do processo do trabalhador em várias instâncias.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Acabei de publicar novas alterações, mas a minha aplicação parece não as ter. Porquê?
Se a sua aplicação utilizar a Cache Local, então tem de reiniciar o seu site para obter as alterações mais recentes. Não quer publicar alterações num local de produção? Consulte as opções de slot na secção de boas práticas anteriores.

> [!NOTE]
> A saída da opção de implantação [de pacotes](deploy-run-package.md) não é compatível com a cache local.

### <a name="where-are-my-logs"></a>Onde estão os meus troncos?
Com a Cache Local, os seus registos e pastas de dados parecem um pouco diferentes. No entanto, a estrutura das suas subpadagens permanece a mesma, exceto que as subpastas estão aninhadas sob uma subpasta com o formato "identificador VM único" + carimbo temporal.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tenho cache local ativado, mas a minha aplicação ainda é reiniciada. Porquê? Pensei que a Cache local ajudava com o recomeço frequente das aplicações.
A Cache local ajuda a evitar o reinício da aplicação relacionada com o armazenamento. No entanto, a sua aplicação ainda pode sofrer recomeçações durante as atualizações planeadas da infraestrutura do VM. A aplicação geral reinicia que você experimenta com Cache Local ativado deve ser menor.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>A Cache Local exclui que os diretórios sejam copiados para a unidade local mais rápida?
Como parte do passo que copia o conteúdo de armazenamento, qualquer pasta que seja nomeada repositório é excluída. Isto ajuda em cenários em que o conteúdo do seu site pode conter um repositório de controlo de origem que pode não ser necessário no funcionamento diário da app. 

### <a name="how-to-flush-the-local-cache-logs-after-a-site-management-operation"></a>Como lavar os registos de cache locais após uma operação de gestão do local?
Para lavar os registos de cache locais, pare e reinicie a aplicação. Esta ação limpa a velha cache. 

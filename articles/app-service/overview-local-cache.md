---
title: Cache local
description: Saiba como o cache local funciona no serviço Azure App e como habilitar, redimensionar e consultar o status do cache local do seu aplicativo.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 87c95d8bbf199f232eca5475f4d8f0c64427a198
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680890"
---
# <a name="azure-app-service-local-cache-overview"></a>Visão geral do cache local do serviço de Azure App

> [!NOTE]
> O cache local não tem suporte em aplicativos de funções ou aplicativos de serviço de aplicativo em contêiner, como no [serviço de aplicativo no Linux](containers/app-service-linux-intro.md).


Azure App conteúdo do serviço é armazenado no armazenamento do Azure e é exibido de maneira durável como um compartilhamento de conteúdo. Esse design destina-se a trabalhar com uma variedade de aplicativos e tem os seguintes atributos:  

* O conteúdo é compartilhado entre várias instâncias de VM (máquina virtual) do aplicativo.
* O conteúdo é durável e pode ser modificado pela execução de aplicativos.
* Arquivos de log e arquivos de dados de diagnóstico estão disponíveis na mesma pasta de conteúdo compartilhado.
* A publicação de novo conteúdo atualiza diretamente a pasta de conteúdo. Você pode exibir imediatamente o mesmo conteúdo por meio do site do SCM e do aplicativo em execução (normalmente, algumas tecnologias, como ASP.NET, iniciam uma reinicialização do aplicativo em algumas alterações de arquivo para obter o conteúdo mais recente).

Embora muitos aplicativos usem um ou todos esses recursos, alguns aplicativos precisam apenas de um armazenamento de conteúdo somente leitura de alto desempenho, que podem ser executados com alta disponibilidade. Esses aplicativos podem se beneficiar de uma instância de VM de um cache local específico.

O recurso de cache local do serviço de Azure App fornece uma exibição de função Web do seu conteúdo. Esse conteúdo é um cache de gravação e descarte do seu conteúdo de armazenamento criado de forma assíncrona no local. Quando o cache estiver pronto, o site será alternado para execução no conteúdo armazenado em cache. Os aplicativos executados no cache local têm os seguintes benefícios:

* Elas estão imunes a latências que ocorrem quando acessam conteúdo no armazenamento do Azure.
* Eles estão imunes a atualizações planejadas ou tempos de inatividade não planejados e quaisquer outras interrupções com o armazenamento do Azure que ocorrem em servidores que atendem ao compartilhamento de conteúdo.
* Eles têm menos reinicializações de aplicativo devido a alterações de compartilhamento de armazenamento.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Como o cache local altera o comportamento do serviço de aplicativo
* _D:\home_ aponta para o cache local, que é criado na instância de VM quando o aplicativo é iniciado. _D:\Local_ continua a apontar para o armazenamento temporário específico da VM.
* O cache local contém uma cópia única das pastas _/site_ e _/siteextensions_ do repositório de conteúdo compartilhado, em _D:\home\site_ e _D:\home\siteextensions_, respectivamente. Os arquivos são copiados para o cache local quando o aplicativo é iniciado. O tamanho das duas pastas para cada aplicativo é limitado a 300 MB por padrão, mas você pode aumentá-lo para até 2 GB. Se os arquivos copiados excederem o tamanho do cache local, o serviço de aplicativo ignorará silenciosamente o cache local e lerá do compartilhamento de arquivos remoto.
* O cache local é de leitura/gravação. No entanto, qualquer modificação é descartada quando o aplicativo move máquinas virtuais ou é reiniciado. Não use o cache local para aplicativos que armazenam dados de missão crítica no repositório de conteúdo.
* _D:\home\LogFiles_ e _D:\home\Data_ contêm arquivos de log e dados de aplicativo. As duas subpastas são armazenadas localmente na instância de VM e são copiadas para o repositório de conteúdo compartilhado periodicamente. Os aplicativos podem persistir arquivos de log e dados gravando-os nessas pastas. No entanto, a cópia para o repositório de conteúdo compartilhado é o melhor esforço, portanto, é possível que os arquivos de log e os dados sejam perdidos devido a uma falha repentina de uma instância de VM.
* O [streaming de log](troubleshoot-diagnostic-logs.md#stream-logs) é afetado pela cópia de melhor esforço. Você pode observar um atraso de um minuto nos logs transmitidos.
* No repositório de conteúdo compartilhado, há uma alteração na estrutura de pastas dos arquivos de _log_ e das pastas de _dados_ para aplicativos que usam o cache local. Agora há subpastas que seguem o padrão de nomenclatura de "identificador exclusivo" + carimbo de data/hora. Cada uma das subpastas corresponde a uma instância de VM em que o aplicativo está em execução ou foi executado.
* Outras pastas no _D:\home_ permanecem no cache local e não são copiadas para o repositório de conteúdo compartilhado.
* A implantação de aplicativo por meio de qualquer método com suporte publica diretamente no repositório de conteúdo compartilhado durável. Para atualizar as pastas _D:\home\site_ e _D:\home\siteextensions_ no cache local, o aplicativo precisa ser reiniciado. Para tornar o ciclo de vida contínuo, consulte as informações mais adiante neste artigo.
* A exibição de conteúdo padrão do site do SCM continua a ser a do repositório de conteúdo compartilhado.

## <a name="enable-local-cache-in-app-service"></a>Habilitar o cache local no serviço de aplicativo
Você configura o cache local usando uma combinação de configurações de aplicativo reservadas. Você pode definir essas configurações de aplicativo usando os seguintes métodos:

* [Portal do Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurar o cache local usando o portal do Azure
<a name="Configure-Local-Cache-Portal"></a>

Você habilita o cache local por aplicativo Web usando esta configuração de aplicativo: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Portal do Azure configurações do aplicativo: cache local](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurar o cache local usando Azure Resource Manager
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

## <a name="change-the-size-setting-in-local-cache"></a>Alterar a configuração de tamanho no cache local
Por padrão, o tamanho do cache local é **300 MB**. Isso inclui as pastas/site e/siteextensions que são copiadas do armazenamento de conteúdo, bem como quaisquer logs e pastas de dados criados localmente. Para aumentar esse limite, use a configuração de aplicativo `WEBSITE_LOCAL_CACHE_SIZEINMB`. Você pode aumentar o tamanho de até **2 GB** (2000 MB) por aplicativo.

## <a name="best-practices-for-using-app-service-local-cache"></a>Práticas recomendadas para usar o cache local do serviço de aplicativo
Recomendamos que você use o cache local em conjunto com o recurso de [ambientes de preparo](../app-service/deploy-staging-slots.md) .

* Adicione a configuração do aplicativo *adesivo* `WEBSITE_LOCAL_CACHE_OPTION` com o valor `Always` ao seu slot de **produção** . Se você estiver usando `WEBSITE_LOCAL_CACHE_SIZEINMB`, adicione-o também como uma configuração adesiva ao seu slot de produção.
* Crie um slot de **preparo** e publique em seu slot de preparo. Normalmente, você não define o slot de preparo para usar o cache local para habilitar um ciclo de vida de desenvolvimento/implantação-teste contínuo para preparo se você obtiver os benefícios do cache local para o slot de produção.
* Teste seu site em relação ao slot de preparo.  
* Quando estiver pronto, emita uma [operação de permuta](../app-service/deploy-staging-slots.md#Swap) entre os slots de preparo e de produção.  
* As configurações adesivas incluem nome e adesivo em um slot. Assim, quando o slot de preparo é alternado para produção, ele herda as configurações do aplicativo de cache local. O slot de produção trocado recentemente será executado no cache local após alguns minutos e será ativado como parte do slot aquecimento após a troca. Assim, quando a permuta do slot for concluída, o slot de produção será executado no cache local.

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Como saber se o cache local se aplica ao meu aplicativo?
Se seu aplicativo precisar de um repositório de conteúdo confiável e de alto desempenho, o não usará o repositório de conteúdo para gravar dados críticos em tempo de execução e tiver menos de 2 GB de tamanho total, a resposta será "Sim"! Para obter o tamanho total de suas pastas/site e/siteextensions, você pode usar a extensão de site "uso de disco de aplicativos Web do Azure".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Como saber se meu site mudou para usar o cache local?
Se você estiver usando o recurso de cache local com ambientes de preparo, a operação de permuta não será concluída até que o cache local seja quente. Para verificar se o site está em execução no cache local, você pode verificar a variável de ambiente do processo de trabalho `WEBSITE_LOCALCACHE_READY`. Use as instruções na página [variável de ambiente do processo de trabalho](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) para acessar a variável de ambiente do processo de trabalho em várias instâncias.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Acabei de publicar novas alterações, mas meu aplicativo parece não tê-las. Porquê?
Se seu aplicativo usar o cache local, você precisará reiniciar o site para obter as alterações mais recentes. Não deseja publicar alterações em um site de produção? Consulte as opções de slot na seção de práticas recomendadas anteriores.

### <a name="where-are-my-logs"></a>Onde estão meus logs?
Com o cache local, os logs e as pastas de dados têm uma aparência um pouco diferente. No entanto, a estrutura de suas subpastas permanece a mesma, exceto que as subpastas são aninhado sob uma subpasta com o formato "Unique VM Identifier" + carimbo de data/hora.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tenho o cache local habilitado, mas meu aplicativo ainda é reiniciado. Porquê? Pensei que o cache local ajudou com reinicializações de aplicativos frequentes.
O cache local ajuda a impedir a reinicialização do aplicativo relacionado ao armazenamento. No entanto, seu aplicativo ainda pode passar por reinicializações durante as atualizações de infraestrutura planejadas da VM. O aplicativo geral reinicia que você enfrenta com o cache local habilitado deve ser menor.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>O cache local exclui qualquer diretório de ser copiado para a unidade local mais rápida?
Como parte da etapa que copia o conteúdo de armazenamento, qualquer pasta denominada repositório é excluída. Isso ajuda com cenários em que o conteúdo do site pode conter um repositório de controle do código-fonte que pode não ser necessário na operação diária do aplicativo. 

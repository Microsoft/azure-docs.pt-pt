---
title: Melhores práticas - serviço de aplicações do Azure
description: Aprenda as melhores práticas e resolução de problemas do serviço de aplicações do Azure.
services: app-service
documentationcenter: ''
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fc3749a9ebfbf0319a57b471b6fce9f62042ba27
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849789"
---
# <a name="best-practices-for-azure-app-service"></a>Melhores Práticas do Serviço de Aplicações do Azure
Este artigo resume as melhores práticas para utilizar [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Partilha de localização
Quando os recursos do Azure compor uma solução como uma aplicação web e uma base de dados estão localizados em regiões diferentes, ele pode ter os seguintes efeitos:

* Maior latência na comunicação entre os recursos
* Custos monetários para dados de saída de transferência entre regiões conforme indicado no [do Azure de página de preços](https://azure.microsoft.com/pricing/details/data-transfers).

A colocalização na mesma região é melhor para compor uma solução como uma aplicação web e uma conta de armazenamento ou base de dados utilizada para armazenar o conteúdo ou dados de recursos do Azure. Quando criar recursos, certifique-se de que estão na mesma região do Azure, a menos que tenha um motivo específico à empresas ou design de não ser. Pode mover uma aplicação de serviço de aplicações para a mesma região que a sua base de dados utilizando o [funcionalidade de clonagem de serviço de aplicações](app-service-web-app-cloning.md) atualmente disponível para aplicações de plano de serviço de aplicações Premium.   

## <a name="memoryresources"></a>Quando aplicações consumam mais memória do que o esperado
Quando que uma aplicação consome mais memória do que o esperado, como indicado por meio de recomendações de monitorização ou serviços específico, considere a [funcionalidade de recuperação automática do serviço de aplicações](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para a funcionalidade de recuperação automática está a demorar ações personalizadas com base no limite de memória. Ações abrangem o espetro de notificações por e-mail para investigação por meio de despejo de memória de atenuação no lugar ao reciclar o processo de trabalho. Recuperação automática pode ser configurado por meio da Web. config bem como através de uma interface do usuário amigável conforme descrito nesta mensagem de blogue para o [extensão de Site de suporte de serviço de aplicações](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando aplicativos consumam mais CPU do que o esperado
Quando observar um aplicativo consome mais CPU do que o esperado ou experiências repetidos picos de CPU como indicado por meio de recomendações de monitorização ou serviços específico, considere aumentar ou reduzir horizontalmente o plano do serviço de aplicações. Se seu aplicativo for com monitoração de estado, o aumento vertical é a única opção, enquanto se seu aplicativo estiver fora sem monitoração de estado, dimensionamento dá-lhe mais flexibilidade e mais alto potencial de dimensionamento. 

Para obter mais informações sobre os aplicativos "sem monitoração de estado" de "com monitoração de estado" vs pode ver este vídeo: [Planeamento de uma aplicação de várias camadas de ponto a ponto escalável no serviço de aplicações do Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre as opções de dimensionamento e o dimensionamento automático do serviço de aplicações, consulte [dimensionar uma aplicação Web no serviço de aplicações do Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Quando os recursos de soquete são esgotados
Um motivo comum para esgotar ligações de saída de TCP é a utilização das bibliotecas de cliente, que não é implementado para reutilizar conexões TCP, ou quando não é utilizado um protocolo de alto nível, como HTTP - Keep-Alive. Reveja a documentação para cada uma das bibliotecas referenciadas pelas aplicações no seu plano de serviço de aplicações para garantir que eles são configurados ou acessados no seu código para reutilização eficiente de ligações de saída. Siga também as diretrizes de documentação da biblioteca para criação adequada e a versão ou a limpeza para evitar a fuga de ligações. Enquanto tais investigações de bibliotecas de cliente estão em curso, o impacto pode ser mitigado ao aumentar horizontalmente para várias instâncias.

### <a name="nodejs-and-outgoing-http-requests"></a>NODE. js e os pedidos de http enviados
Ao trabalhar com o node. js e muitos pedidos de http de saída, é importante lidar com HTTP - Keep-Alive. Pode utilizar o [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` pacote para tornar mais fácil no seu código.

Processar sempre o `http` resposta, mesmo se não fazer nada no manipulador. Se não processar a resposta corretamente, a aplicação fica bloqueada, eventualmente, porque não existem soquetes mais disponíveis.

Por exemplo, ao trabalhar com o `http` ou `https` pacote:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Se estiver a executar no serviço de aplicações no Linux numa máquina com vários núcleos, outra prática recomendada é usar PM2 para iniciar vários processos de node. js para executar seu aplicativo. Pode fazê-lo ao especificar um comando de arranque para o seu contentor.

Por exemplo, para começar a quatro instâncias:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Quando a aplicação criar cópias de segurança começa a falhar
Os dois motivos mais comuns, por que está a falha de cópia de segurança de aplicação: definições de armazenamento inválido e a configuração de base de dados inválido. Estas falhas normalmente ocorrem quando há alterações aos recursos de armazenamento ou base de dados ou as alterações para saber como aceder a estes recursos (por exemplo, credenciais atualizadas para a base de dados selecionada nas definições de cópia de segurança). Normalmente, as cópias de segurança executam numa agenda e necessitam de acesso a bancos de dados e de armazenamento (para produzir os ficheiros de cópia de segurança) (para copiar e ler o conteúdo a serem incluídos na cópia de segurança). O resultado da falha ao aceder a qualquer um destes recursos seria falha de cópia de segurança consistente. 

Quando ocorrem falhas de cópia de segurança, reveja os resultados mais recentes para compreender o tipo de falha que está acontecendo. Para falhas de acesso de armazenamento, rever e atualizar as definições de armazenamento utilizadas na configuração de cópia de segurança. Para falhas de acesso de base de dados, rever e atualizar suas cadeias de caracteres de ligações como parte das definições da aplicação; em seguida, avance para atualizar a configuração de cópia de segurança para incluir corretamente as bases de dados necessárias. Para obter mais informações sobre cópias de segurança de aplicação, consulte [cópia de segurança numa aplicação web no App Service do Azure](manage-backup.md).

## <a name="nodejs"></a>Quando novas de node. js aplicações são implementadas no serviço de aplicações do Azure
Configuração de padrão do serviço de aplicações do Azure para aplicações node. js destina-se que melhor se adapte às necessidades das aplicações mais comuns. Se a configuração para a sua aplicação node. js beneficiariam com a otimização personalizadas para melhorar o desempenho ou otimizar a utilização de recursos para os recursos de CPU/memória/rede, consulte [melhores práticas e guia de resolução de problemas para as aplicações de nó nas aplicações do Azure Serviço](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Este artigo descreve as definições do iisnode, poderá ter de configurar para a sua aplicação node. js, descreve os vários cenários ou problemas que a sua aplicação possa estar a deparar e mostra como resolver estes problemas.


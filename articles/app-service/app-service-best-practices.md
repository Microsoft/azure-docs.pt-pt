---
title: Melhores práticas
description: Aprenda as melhores práticas e os cenários comuns de resolução de problemas para a sua aplicação em execução no Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: 91fd974c730037907258cb4a670f6fa836bfda6c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144875"
---
# <a name="best-practices-for-azure-app-service"></a>Melhores Práticas do Serviço de Aplicações do Azure
Este artigo resume as melhores práticas para a utilização [do Azure App Service](./overview.md). 

## <a name="colocation"></a><a name="colocation"></a>Colocação
Quando os recursos Azure que compõem uma solução como uma aplicação web e uma base de dados estão localizados em diferentes regiões, pode ter os seguintes efeitos:

* Aumento da latência na comunicação entre recursos
* Encargos monetários relativos à transferência de dados entre regiões, conforme assinalado na página de preços do [Azure](https://azure.microsoft.com/pricing/details/data-transfers).

A colocação na mesma região é o melhor para os recursos Azure compondo uma solução como uma aplicação web e uma base de dados ou conta de armazenamento usada para conter conteúdo ou dados. Ao criar recursos, certifique-se de que estão na mesma região de Azure, a menos que tenha razões específicas para que não o sejam. Pode mover uma aplicação do Serviço de Aplicações para a mesma região que a sua base de dados, utilizando [a funcionalidade de clonagem do Serviço de Aplicações](app-service-web-app-cloning.md) atualmente disponível para aplicações Premium App Service Plan.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Quando as aplicações consomem mais memória do que o esperado
Quando notar que uma aplicação consome mais memória do que o esperado, como indicado através de recomendações de monitorização ou serviço, considere a [funcionalidade de Auto-Cura](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites)do Serviço de Aplicações . Uma das opções para a funcionalidade de auto-cura é tomar ações personalizadas com base num limiar de memória. As ações abrangem o espectro desde notificações de e-mail até investigação através do despejo de memória até à mitigação no local, reciclando o processo de trabalhador. A cicatrização automática pode ser configurada através de web.config e através de uma interface de utilizador amigável, conforme descrito neste post de blog para a Extensão do [Site de Suporte ao Serviço de Aplicações.](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Quando as aplicações consomem mais CPU do que o esperado
Quando notar que uma aplicação consome mais CPU do que o esperado ou experimenta picos repetidos de CPU, conforme indicado através de recomendações de monitorização ou serviço, considere aumentar ou expandir o plano de Serviço de Aplicações. Se a sua aplicação for imponente, o escalonamento é a única opção, enquanto que se a sua aplicação for apátrida, a escala dá-lhe mais flexibilidade e um potencial de maior escala. 

Para mais informações sobre aplicações "stateful" vs "apátridas" pode ver este vídeo: [Planejando uma aplicação multi-tier scalable de ponta a ponta no Serviço de Aplicações Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre opções de dimensionamento e autoscaling do Serviço de Aplicações, consulte [Scale a Web App in Azure App Service](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>Quando os recursos da tomada estão esgotados
Uma razão comum para esgotar as ligações TCP de saída é a utilização de bibliotecas de clientes, que não são implementadas para reutilizar ligações TCP, ou quando um protocolo de nível superior, como HTTP - Keep-Alive não é utilizado. Reveja a documentação de cada uma das bibliotecas referenciadas pelas aplicações no seu Plano de Serviço de Aplicações para garantir que são configuradas ou acedidas no seu código para uma reutilização eficiente das ligações de saída. Siga também as orientações de documentação da biblioteca para a criação e libertação ou limpeza adequadas para evitar fugas de ligações. Embora tais investigações de bibliotecas de clientes estejam em andamento, o impacto pode ser atenuado escalando para vários casos.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js e pedidos http de saída
Ao trabalhar com Node.js e muitos pedidos de http outgoing, lidar com HTTP - Keep-Alive é importante. Pode utilizar o pacote [de agentekeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` para facilitar o seu código.

Lide sempre com a `http` resposta, mesmo que não faça nada no manipulador. Se não manusear a resposta corretamente, a sua aplicação fica presa eventualmente porque não há mais tomadas disponíveis.

Por exemplo, ao trabalhar com o `http` ou `https` pacote:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Se estiver a executar o Serviço de Aplicações no Linux numa máquina com vários núcleos, outra das melhores práticas é usar o PM2 para iniciar vários processos Node.js para executar a sua aplicação. Pode fazê-lo especificando um comando de arranque para o seu recipiente.

Por exemplo, para iniciar quatro instâncias:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Quando a sua cópia de segurança da aplicação começar a falhar
As duas razões mais comuns pelas quais a cópia de segurança da aplicação falha são: configurações de armazenamento inválidas e configuração de base de dados inválida. Estas falhas normalmente acontecem quando há alterações nos recursos de armazenamento ou base de dados, ou alterações de como aceder a estes recursos (por exemplo, credenciais atualizadas para a base de dados selecionadas nas definições de backup). As cópias de segurança normalmente funcionam num horário e exigem o acesso ao armazenamento (para a saída dos ficheiros com cópias) e bases de dados (para copiar e ler conteúdos a incluir na cópia de segurança). O resultado de não aceder a nenhum destes recursos seria uma falha consistente de backup. 

Quando as falhas de backup acontecerem, reveja os resultados mais recentes para entender que tipo de falha está a acontecer. Para falhas de acesso ao armazenamento, reveja e atualize as definições de armazenamento utilizadas na configuração de backup. Para falhas de acesso à base de dados, reveja e atualize as cordas das suas ligações como parte das definições das aplicações; em seguida, proceda à atualização da sua configuração de backup para incluir adequadamente as bases de dados necessárias. Para obter mais informações sobre cópias de segurança de aplicações, consulte [Uma aplicação web no Azure App Service](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Quando novas aplicações Node.js são implementadas no Azure App Service
A configuração padrão do Azure App Service para Node.js aplicações destina-se a atender melhor às necessidades das aplicações mais comuns. Se a configuração para a sua aplicação Node.js beneficiar de afinação personalizada para melhorar o desempenho ou otimizar o uso de recursos para os recursos da CPU/memória/rede, consulte [as melhores práticas e guia de resolução de problemas para aplicações de nó no Azure App Service](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Este artigo descreve as definições de iisnode que poderá necessitar de configurar para a sua aplicação Node.js, descreve os vários cenários ou problemas que a sua aplicação pode estar a enfrentar e mostra como lidar com estes problemas.


## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as melhores práticas, visite [o App Service Diagnostics](./overview-diagnostics.md) para descobrir as melhores práticas ações específicas do seu recurso.

- Navegue para a sua Web App no [portal Azure.](https://portal.azure.com)
- Clique no **Diagnóstico e resolva problemas** na navegação à esquerda, que abre o App Service Diagnostics.
- Escolha o azulejo inicial **das Melhores Práticas.**
- Clique em **Melhores Práticas para Disponibilidade & Desempenho** ou **Melhores Práticas para Configuração Ideal** para ver o estado atual da sua aplicação no que diz respeito a estas melhores práticas.

Também pode utilizar este link para abrir diretamente os Diagnósticos de Serviço de Aplicações para o seu `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` recurso:
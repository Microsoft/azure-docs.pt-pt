---
title: Melhores práticas
description: Aprenda as melhores práticas e os cenários comuns de resolução de problemas para a sua aplicação em execução no Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: ded812d5d7a0440466e7284b56c90965ea00406e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75768491"
---
# <a name="best-practices-for-azure-app-service"></a>Melhores Práticas do Serviço de Aplicações do Azure
Este artigo resume as melhores práticas para a utilização do Serviço de [Aplicações Azure.](https://go.microsoft.com/fwlink/?LinkId=529714) 

## <a name="colocation"></a><a name="colocation"></a>Colocalização
Quando os recursos do Azure compõem uma solução como uma aplicação web e uma base de dados estão localizadas em diferentes regiões, pode ter os seguintes efeitos:

* Maior latência na comunicação entre recursos
* Encargos monetários para a transferência de dados de saída transporam regiões, tal como se nota na página de [preços do Azure.](https://azure.microsoft.com/pricing/details/data-transfers)

A colocalização na mesma região é o melhor para os recursos do Azure que compõem uma solução como uma aplicação web e uma conta de base de dados ou armazenamento usada para conter conteúdo ou dados. Ao criar recursos, certifique-se de que estão na mesma região de Azure, a menos que tenha razões específicas para não estar. Pode mover uma aplicação do App Service para a mesma região que a sua base de dados utilizando a funcionalidade de clonagem do [Serviço de Aplicações](app-service-web-app-cloning.md) atualmente disponível para aplicações premium app Service Plan.   

## <a name="when-apps-consume-more-memory-than-expected"></a><a name="memoryresources"></a>Quando as aplicações consomem mais memória do que o esperado
Quando notar que uma aplicação consome mais memória do que o esperado, como indicado através de recomendações de monitorização ou de serviço, considere a funcionalidade de Cura Automática do [Serviço de Aplicações](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para a funcionalidade de Cura Automática é tomar ações personalizadas com base num limiar de memória. As ações abrangem o espectro desde notificações de e-mail até à investigação através de despejo de memória até à mitigação no local, reciclando o processo do trabalhador. A cicatrização automática pode ser configurada através de web.config e através de uma interface de utilizador amigável, como descrito neste post de blog para a extensão do site de suporte ao serviço de [aplicações.](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps)   

## <a name="when-apps-consume-more-cpu-than-expected"></a><a name="CPUresources"></a>Quando as aplicações consomem mais CPU do que o esperado
Quando notar que uma aplicação consome mais CPU do que o esperado ou experimenta picos de CPU repetidos, conforme indicado através de recomendações de monitorização ou de serviço, considere escalonar ou escalonar o plano de Serviço de Aplicações. Se a sua aplicação for audatória, a escala é a única opção, enquanto que se a sua aplicação for apátrida, a escala ção dá-lhe mais flexibilidade e maior potencial de escala. 

Para mais informações sobre aplicações "apátridas" vs "apátridas" pode ver este vídeo: [Planejando uma aplicação de vários níveis escalável no Serviço de Aplicações Azure](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre opções de escalae e autoscalcificação do Serviço de Aplicações, consulte [Scale a Web App no Serviço de Aplicações Azure](manage-scale-up.md).  

## <a name="when-socket-resources-are-exhausted"></a><a name="socketresources"></a>Quando os recursos da tomada estão esgotados
Uma razão comum para esgotar as ligações TCP de saída é a utilização de bibliotecas de clientes, que não são implementadas para reutilizar ligações TCP, ou quando não é utilizado um protocolo de nível superior como http - Keep-Alive. Reveja a documentação de cada uma das bibliotecas referenciadas pelas aplicações no seu Plano de Serviço de Aplicações para garantir que são configuradas ou acedidas no seu código para uma reutilização eficiente das ligações de saída. Siga também a orientação da documentação da biblioteca para a criação e libertação ou limpeza adequadas para evitar fugas de ligações. Embora tais investigações de bibliotecas de clientes estejam em curso, o impacto pode ser atenuado através da escala para vários casos.

### <a name="nodejs-and-outgoing-http-requests"></a>Node.js e pedidos de http de saída
Ao trabalhar com o Node.js e muitos pedidos de http de saída, lidar com http - Keep-Alive é importante. Pode usar o pacote [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) `npm` para facilitar o seu código.

Manuseie sempre a `http` resposta, mesmo que não faça nada no manipulador. Se não manusear corretamente a resposta, a sua aplicação fica presa eventualmente porque não há mais tomadas disponíveis.

Por exemplo, quando `http` se `https` trabalha com o ou o pacote:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Se estiver a executar o Serviço de Aplicações no Linux numa máquina com vários núcleos, outra melhor prática é usar o PM2 para iniciar vários processos nonómetros para executar a sua aplicação. Pode fazê-lo especificando um comando de arranque no seu recipiente.

Por exemplo, para iniciar quatro instâncias:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="when-your-app-backup-starts-failing"></a><a name="appbackup"></a>Quando a cópia de segurança da sua aplicação começa a falhar
As duas razões mais comuns para a falha na cópia de segurança da aplicação são: configurações de armazenamento inválidas e configuração de bases de dados inválidas. Estas falhas normalmente acontecem quando há alterações nos recursos de armazenamento ou base de dados, ou alterações na forma de aceder a esses recursos (por exemplo, credenciais atualizadas para a base de dados selecionada sintetizadas nas definições de backup). As cópias de segurança normalmente funcionam num horário e exigem o acesso ao armazenamento (para a colocação dos ficheiros de reserva) e bases de dados (para copiar e ler conteúdos a incluir na cópia). O resultado de não aceder a nenhum destes recursos seria uma falha consistente de backup. 

Quando as falhas de backup acontecerem, reveja os resultados mais recentes para perceber que tipo de falha está a acontecer. Para falhas de acesso ao armazenamento, reveja e atualize as definições de armazenamento utilizadas na configuração de backup. Para falhas de acesso à base de dados, reveja e atualize as cordas das suas ligações como parte das definições da aplicação; em seguida, proceda à atualização da sua configuração de backup para incluir corretamente as bases de dados necessárias. Para obter mais informações sobre cópias de segurança de apps, consulte [a backup de uma aplicação web no Azure App Service](manage-backup.md).

## <a name="when-new-nodejs-apps-are-deployed-to-azure-app-service"></a><a name="nodejs"></a>Quando novas aplicações noNó.js são implementadas para o Serviço de Aplicações Azure
A configuração padrão do Serviço de Aplicações Azure para aplicações Node.js destina-se a melhor atender às necessidades das aplicações mais comuns. Se a configuração da sua app Node.js beneficiar de afinação personalizada para melhorar o desempenho ou otimizar o uso de recursos de recursos cpu/memória/rede, consulte [as melhores práticas e guia de resolução de problemas para aplicações](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)de nó no Serviço de Aplicações Azure . Este artigo descreve as definições de iisnode que poderá ter de configurar para a sua aplicação Node.js, descreve os vários cenários ou problemas que a sua aplicação pode estar a enfrentar e mostra como lidar com estes problemas.


## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as melhores práticas, visite [o App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) para descobrir as melhores práticas atol específicas do seu recurso.

- Navegue para a sua Web App no [portal Azure](https://portal.azure.com).
- Clique em **Diagnosticar e resolver problemas** na navegação à esquerda, o que abre diagnósticos de serviço de aplicações.
- Escolha o azulejo da página inicial **das Melhores Práticas.**
- Clique em **Boas Práticas para Disponibilidade & Desempenho** ou Boas **Práticas para Uma Configuração Ideal** para ver o estado atual da sua aplicação no que diz respeito a estas boas práticas.

Também pode utilizar este link para abrir diretamente os `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`Diagnósticos do Serviço de Aplicações para o seu recurso: .
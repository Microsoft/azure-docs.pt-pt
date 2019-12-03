---
title: Melhores Práticas
description: Conheça as práticas recomendadas e os cenários de solução de problemas comuns para seu aplicativo em execução no Azure App Service.
author: dariagrigoriu
ms.assetid: f3359464-fa44-4f4a-9ea6-7821060e8d0d
ms.topic: article
ms.date: 07/01/2016
ms.author: dariac
ms.custom: seodec18
ms.openlocfilehash: 328e0c882ea2fb3860663e04b88488bd54339c75
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671499"
---
# <a name="best-practices-for-azure-app-service"></a>Melhores práticas do Serviço de Aplicações do Azure
Este artigo resume as práticas recomendadas para o uso do [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Colocação
Quando os recursos do Azure que compõem uma solução como um aplicativo Web e um banco de dados estão localizados em regiões diferentes, ele pode ter os seguintes efeitos:

* Maior latência na comunicação entre os recursos
* Cobranças monetárias para transferência de dados de saída entre regiões, conforme observado na [página de preços do Azure](https://azure.microsoft.com/pricing/details/data-transfers).

A colocação na mesma região é melhor para recursos do Azure que compõem uma solução como um aplicativo Web e um banco de dados ou uma conta de armazenamento usada para armazenar conteúdo ou dados. Ao criar recursos, verifique se eles estão na mesma região do Azure, a menos que você tenha um motivo de design ou de negócios específico para que eles não sejam. Você pode mover um aplicativo do serviço de aplicativo para a mesma região do banco de dados usando o [recurso de clonagem do serviço de aplicativo](app-service-web-app-cloning.md) disponível atualmente para aplicativos do plano do serviço de aplicativo Premium.   

## <a name="memoryresources"></a>Quando os aplicativos consomem mais memória do que o esperado
Quando você percebe que um aplicativo consome mais memória do que o esperado, conforme indicado por meio de monitoramento ou recomendações de serviço, considere o [recurso de auto-recuperação do serviço de aplicativo](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para o recurso de recuperação automática está realizando ações personalizadas com base em um limite de memória. As ações abrangem o espectro de notificações por email para investigação por meio do despejo de memória para a mitigação no local, reciclando o processo de trabalho. A auto-recuperação pode ser configurada via Web. config e por meio de uma interface de usuário amigável, conforme descrito nesta postagem de blog para a [extensão do site de suporte do serviço de aplicativo](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando os aplicativos consomem mais CPU do que o esperado
Quando você percebe que um aplicativo consome mais CPU do que o esperado ou experimenta picos de CPU repetidos conforme indicado por meio de monitoramento ou recomendações de serviço, considere escalar verticalmente ou escalar horizontalmente o plano do serviço de aplicativo. Se seu aplicativo estiver com monitoração de estado, escalar verticalmente é a única opção, enquanto que, se seu aplicativo estiver sem estado, escalar horizontalmente proporcionará maior flexibilidade e maior potencial de dimensionamento. 

Para obter mais informações sobre aplicativos "com estado" vs "sem estado", você pode assistir a este vídeo: [planejando um aplicativo de várias camadas de ponta a ponta escalonável no serviço de Azure app](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre o dimensionamento do serviço de aplicativo e opções de dimensionamento automático, consulte [dimensionar um aplicativo Web no serviço de Azure app](manage-scale-up.md).  

## <a name="socketresources"></a>Quando os recursos de soquete estão esgotados
Um motivo comum para esgotar as conexões TCP de saída é o uso de bibliotecas de cliente, que não são implementadas para reutilizar conexões TCP ou quando um protocolo de nível superior, como HTTP-Keep-Alive, não é usado. Examine a documentação de cada uma das bibliotecas referenciadas pelos aplicativos em seu plano do serviço de aplicativo para garantir que elas sejam configuradas ou acessadas em seu código para reutilização eficiente de conexões de saída. Além disso, siga as diretrizes de documentação da biblioteca para criação e liberação ou limpeza adequadas para evitar vazamento de conexões. Embora essas investigações de bibliotecas de cliente estejam em andamento, o impacto pode ser reduzido com a expansão para várias instâncias.

### <a name="nodejs-and-outgoing-http-requests"></a>Node. js e solicitações HTTP de saída
Ao trabalhar com o Node. js e muitas solicitações HTTP de saída, lidar com HTTP-Keep-Alive é importante. Você pode usar o pacote de `npm` do [agentkeepalive](https://www.npmjs.com/package/agentkeepalive) para facilitar seu código.

Sempre manipule a resposta `http`, mesmo que você não faça nada no manipulador. Se você não tratar a resposta corretamente, seu aplicativo ficará parado eventualmente porque não há mais soquetes disponíveis.

Por exemplo, ao trabalhar com o pacote de `http` ou `https`:

```javascript
const request = https.request(options, function(response) {
    response.on('data', function() { /* do nothing */ });
});
```

Se você estiver executando o serviço de aplicativo no Linux em um computador com vários núcleos, outra prática recomendada é usar o PM2 para iniciar vários processos do node. js para executar o aplicativo. Você pode fazer isso especificando um comando de inicialização para seu contêiner.

Por exemplo, para iniciar quatro instâncias:

```
pm2 start /home/site/wwwroot/app.js --no-daemon -i 4
```

## <a name="appbackup"></a>Quando o backup do aplicativo começa a falhar
Os dois motivos mais comuns para o backup do aplicativo falhar são: configurações de armazenamento inválidas e configuração de banco de dados inválida. Essas falhas normalmente ocorrem quando há alterações no armazenamento ou em recursos de banco de dados, ou alterações de como acessar esses recursos (por exemplo, credenciais atualizadas para o banco de dados selecionado nas configurações de backup). Os backups normalmente são executados em um agendamento e exigem acesso ao armazenamento (para a saída de arquivos de backup) e bancos de dados (para copiar e ler o conteúdo a ser incluído no backup). O resultado da falha ao acessar qualquer um desses recursos seria uma falha de backup consistente. 

Quando ocorrerem falhas de backup, examine os resultados mais recentes para entender qual tipo de falha está ocorrendo. Para falhas de acesso de armazenamento, examine e atualize as configurações de armazenamento usadas na configuração de backup. Para falhas de acesso ao banco de dados, examine e atualize suas cadeias de conexão como parte das configurações do aplicativo; em seguida, continue a atualizar sua configuração de backup para incluir corretamente os bancos de dados necessários. Para obter mais informações sobre backups de aplicativos, consulte [fazer backup de um aplicativo Web no serviço Azure app](manage-backup.md).

## <a name="nodejs"></a>Quando novos aplicativos node. js são implantados no serviço Azure App
Azure App configuração padrão do serviço para aplicativos node. js destina-se melhor às necessidades dos aplicativos mais comuns. Se a configuração do seu aplicativo node. js se beneficiar do ajuste personalizado para melhorar o desempenho ou otimizar o uso de recursos para CPU/memória/recursos de rede, consulte [práticas recomendadas e guia de solução de problemas para aplicativos de nó no serviço de Azure app](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md). Este artigo descreve as configurações de iisnode que talvez você precise configurar para seu aplicativo node. js, descreve os vários cenários ou problemas que seu aplicativo pode estar enfrentando e mostra como resolver esses problemas.


---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de fila - Armazenamento Azure
description: Uma lista de práticas comprovadas para uso com armazenamento de fila no desenvolvimento de aplicações de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75750511"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento de fila

A Microsoft desenvolveu uma série de práticas comprovadas para desenvolver aplicações de alto desempenho com armazenamento de fila. Esta lista de verificação identifica práticas-chave que os desenvolvedores podem seguir para otimizar o desempenho. Tenha em mente estas práticas enquanto está a desenhar a sua aplicação e durante todo o processo.

O Azure Storage tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os alvos de escalabilidade do Armazenamento Azure, consulte os objetivos de [escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) e metas de escalabilidade e desempenho para armazenamento de [filas.](scalability-targets.md)

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza práticas comprovadas para o desempenho numa lista de verificação que pode seguir enquanto desenvolve a sua aplicação de armazenamento de fila.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Pode projetar a sua aplicação para usar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Está a evitar aproximar-se dos limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm largura de banda suficientemente elevada e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm uma ligação de rede de alta qualidade?](#link-quality) |
| &nbsp; |Redes |[A aplicação do cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto ao Cliente |[Está a utilizar assinaturas de acesso partilhado (SAS) e partilha de recursos de origem cruzada (CORS) para permitir o acesso direto ao Armazenamento Azure?](#sas-and-cors) |
| &nbsp; |configuração .NET |[Está a utilizar .NET Core 2.1 ou mais tarde para um desempenho ótimo?](#use-net-core) |
| &nbsp; |configuração .NET |[Já configurou o seu cliente para utilizar um número suficiente de ligações simultâneas?](#increase-default-connection-limit) |
| &nbsp; |configuração .NET |[Para aplicações .NET, configurou .NET para utilizar um número suficiente de fios?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Assegurou que o paralelismo está devidamente limitado para que não sobrecarregue as capacidades do seu cliente ou se aproxime dos alvos de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Está a utilizar as versões mais recentes das bibliotecas e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Estás a usar uma política de retry com um recuo exponencial para erros de estrangulamento e intervalos?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[A sua aplicação está a evitar tentativas de erros não retáveis?](#non-retryable-errors) |
| &nbsp; |Configuração |[Desligou o algoritmo de Nagle para melhorar o desempenho de pequenos pedidos?](#disable-nagle) |
| &nbsp; |Tamanho da mensagem |[As suas mensagens são compactas para melhorar o desempenho da fila?](#message-size) |
| &nbsp; |Recuperação a granel |[Estás a recuperar várias mensagens numa única operação GET?](#batch-retrieval) |
| &nbsp; |Frequência de sondagens |[Está a votar com frequência para reduzir a latência percebida da sua candidatura?](#queue-polling-interval) |
| &nbsp; |Mensagem de atualização |[Está a utilizar a operação Update Message para armazenar o progresso no processamento de mensagens, para evitar ter de reprocessar toda a mensagem se ocorrer um erro?](#use-update-message) |
| &nbsp; |Arquitetura |[Está a usar filas para tornar toda a sua aplicação mais escalável, mantendo as cargas de trabalho de longo prazo fora do caminho crítico e da escala e depois de forma independente?](#application-architecture) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se a sua aplicação se aproximar ou exceder qualquer um dos alvos de escalabilidade, poderá encontrar tardios de transação acrescidos ou estrangulamentos. Quando o Azure Storage acelera a sua aplicação, o serviço começa a devolver 503 códigos de erro (Servidor ocupado) ou 500 (tempo de funcionamento). Evitar estes erros mantendo-se dentro dos limites dos objetivos de escalabilidade é uma parte importante para melhorar o desempenho da sua aplicação.

Para obter mais informações sobre os alvos de escalabilidade para o serviço de fila, consulte [a escalabilidade do Armazenamento Azure e os alvos de desempenho](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se se aproximar do número máximo de contas de armazenamento permitidas para uma determinada combinação de subscrição/região, está a usar várias contas de armazenamento para aumentar a entrada, a saída, as operações de I/S por segundo (IOPS) ou a capacidade? Neste cenário, a Microsoft recomenda que aproveite os limites acrescidos para as contas de armazenamento para reduzir o número de contas de armazenamento necessárias para a sua carga de trabalho, se possível. Contacte o [Suporte Azure](https://azure.microsoft.com/support/options/) para solicitar limites acrescidos para a sua conta de armazenamento. Para mais informações, consulte Anunciar contas de [armazenamento maiores e de maior escala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e transação

Se a sua aplicação estiver a aproximar-se dos objetivos de escalabilidade para uma única conta de armazenamento, considere adotar uma das seguintes abordagens:  

- Se os alvos de escalabilidade para as filas forem insuficientes para a sua aplicação, utilize várias filas e distribua mensagens por elas.
- Reconsidere a carga de trabalho que faz com que a sua aplicação se aproxime ou exceda o alvo de escalabilidade. Pode desenhá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
- Se a sua aplicação tiver de exceder um dos alvos de escalabilidade, crie várias contas de armazenamento e partifique os seus dados de aplicação através dessas várias contas de armazenamento. Se utilizar este padrão, certifique-se de que projeta a sua aplicação para que possa adicionar mais contas de armazenamento no futuro para equilibrar a carga. As próprias contas de armazenamento não têm qualquer custo que não seja a sua utilização em termos de dados armazenados, transações efetuadas ou dados transferidos.
- Se a sua aplicação estiver a aproximar-se dos alvos da largura de banda, considere comprimir os dados do lado do cliente para reduzir a largura de banda necessária para enviar os dados para o Armazenamento Azure.
    Embora os dados comprimidos possam poupar a largura de banda e melhorar o desempenho da rede, também pode ter efeitos negativos no desempenho. Avaliar o impacto do desempenho dos requisitos adicionais de processamento para a compressão de dados e descompressão do lado do cliente. Tenha em mente que armazenar dados comprimidos pode dificultar a resolução de problemas porque pode ser mais desafiante ver os dados usando ferramentas padrão.
- Se a sua aplicação estiver a aproximar-se dos alvos de escalabilidade, certifique-se de que está a utilizar um backoff exponencial para repetições. É melhor tentar evitar atingir os objetivos de escalabilidade implementando as recomendações descritas neste artigo. No entanto, usar um backoff exponencial para repetições impedirá a sua aplicação de retentar rapidamente, o que pode piorar o estrangulamento. Para mais informações, consulte a secção intitulada [Timeout e Server Busy errors](#timeout-and-server-busy-errors).

## <a name="networking"></a>Redes

Os constrangimentos físicos da rede da aplicação podem ter um impacto significativo no desempenho. As seguintes secções descrevem algumas das limitações que os utilizadores podem encontrar.  

### <a name="client-network-capability"></a>Capacidade de rede de clientes

A largura de banda e a qualidade da ligação de rede desempenham papéis importantes no desempenho da aplicação, conforme descrito nas seguintes secções.

#### <a name="throughput"></a>Débito

Para a largura de banda, o problema é muitas vezes as capacidades do cliente. As instâncias azure maiores têm NICs com maior capacidade, por isso deve considerar a utilização de uma instância maior ou mais VMs se precisar de limites de rede mais elevados a partir de uma única máquina. Se estiver a aceder ao Azure Storage a partir de uma aplicação no local, então aplica-se a mesma regra: compreender as capacidades de rede do dispositivo cliente e a conectividade da rede com a localização do Armazenamento Azure e melhorá-las conforme necessário ou projetar a sua aplicação para trabalhar dentro das suas capacidades.

#### <a name="link-quality"></a>Qualidade de ligação

Como em qualquer utilização da rede, lembre-se que as condições de rede que resultam em erros e perda de pacotes irão retardar a entrada eficaz.  A utilização da WireShark ou da NetMon pode ajudar a diagnosticar este problema.  

### <a name="location"></a>Localização

Em qualquer ambiente distribuído, colocar o cliente perto do servidor proporciona o melhor desempenho. Para aceder ao Azure Storage com a latência mais baixa, a melhor localização para o seu cliente encontra-se dentro da mesma região azure. Por exemplo, se tiver uma aplicação web Azure que utiliza o Armazenamento Azure, localize-as ambas numa única região, como o Us West ou a Ásia Southeast. A co-localização de recursos reduz a latência e o custo, uma vez que a utilização da largura de banda numa única região é gratuita.  

Se as aplicações dos clientes acederem ao Armazenamento Azure, mas não estiverem alojadas no Azure, como aplicações de dispositivos móveis ou em serviços empresariais no local, a localização da conta de armazenamento numa região próxima desses clientes pode reduzir a latência. Se os seus clientes forem amplamente distribuídos (por exemplo, alguns na América do Norte, e alguns na Europa), então considere usar uma conta de armazenamento por região. Esta abordagem é mais fácil de implementar se os dados que as lojas de aplicação são específicos dos utilizadores individuais, e não requer a replicação de dados entre contas de armazenamento.

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que precisa autorizar códigos como o JavaScript que está a ser gerido no navegador web de um utilizador ou numa aplicação para telemóvel para aceder a dados no Armazenamento Do Azure. Uma abordagem é construir uma aplicação de serviço que atue como um representante. O dispositivo do utilizador autentica-se com o serviço, que por sua vez autoriza o acesso aos recursos de Armazenamento Azure. Desta forma, pode evitar expor as chaves da sua conta de armazenamento em dispositivos inseguros. No entanto, esta abordagem coloca uma sobrecarga significativa na aplicação de serviço, uma vez que todos os dados transferidos entre o dispositivo do utilizador e o Armazenamento Azure devem passar pela aplicação de serviço.

Pode evitar utilizar uma aplicação de serviço como procuração para o Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS). Utilizando o SAS, pode ativar o dispositivo do utilizador para fazer pedidos diretamente ao Armazenamento Azure, utilizando um sinal de acesso limitado. Por exemplo, se um utilizador quiser enviar uma fotografia para a sua aplicação, então a sua aplicação de serviço pode gerar um SAS e enviá-la para o dispositivo do utilizador. O token SAS pode conceder permissão para escrever a um recurso de armazenamento Azure por um intervalo de tempo especificado, após o qual expira o token SAS. Para obter mais informações sobre o SAS, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  

Normalmente, um navegador web não permitirá o JavaScript numa página que é hospedada por um site num domínio para realizar determinadas operações, como operações de escrita, para outro domínio. Conhecida como a política de origem mesma, esta política impede que um script malicioso numa página obtenha acesso a dados noutra página web. No entanto, a política de origem pode ser uma limitação na construção de uma solução na nuvem. A partilha de recursos de origem cruzada (CORS) é uma funcionalidade de navegador que permite ao domínio alvo comunicar ao navegador que confia em pedidos originários do domínio de origem.

Por exemplo, suponha que uma aplicação web em funcionamento em Azure faz um pedido de um recurso para uma conta de Armazenamento Azure. A aplicação web é o domínio de origem, e a conta de armazenamento é o domínio alvo. Pode configurar o CORS para qualquer um dos serviços de Armazenamento Azure para comunicar ao navegador web que os pedidos do domínio de origem são confiáveis pelo Armazenamento Azure. Para obter mais informações sobre o CORS, consulte o suporte de [partilha de recursos de origem cruzada (CORS) para o Armazenamento Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Tanto o SAS como o CORS podem ajudá-lo a evitar cargas desnecessárias na sua aplicação web.  

## <a name="net-configuration"></a>configuração .NET

Se utilizar a .NET Framework, esta secção lista várias configurações de configuração rápida que pode utilizar para fazer melhorias significativas de desempenho.  Se utilizar outras línguas, verifique se conceitos semelhantes se aplicam na sua língua escolhida.  

### <a name="use-net-core"></a>Use .NET Core

Desenvolva as suas aplicações de Armazenamento Azure com .NET Core 2.1 ou mais tarde para tirar partido das melhorias de desempenho. Recomenda-se a utilização do Núcleo 3.x .NET 3.x quando possível.

Para obter mais informações sobre melhorias de desempenho em .NET Core, consulte as seguintes publicações de blog:

- [Melhorias de desempenho em .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho em .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar o limite de ligação por defeito

Em .NET, o seguinte código aumenta o limite de ligação padrão (que normalmente é 2 em ambiente de cliente ou 10 em ambiente de servidor) para 100. Normalmente, deve definir o valor para aproximadamente o número de fios utilizados pela sua aplicação.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Detete o limite de ligação antes de abrir quaisquer ligações.  

Para outras linguagens de programação, consulte a documentação dessa linguagem para determinar como definir o limite de ligação.  

Para mais informações, consulte o blog post [Web Services: Conexões Simultâneas](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de fios

Se estiver a utilizar chamadas sincronizadas juntamente com tarefas assíncronas, pode querer aumentar o número de fios na piscina de fios:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para mais informações, consulte o método [ThreadPool.SetMinThreads.](/dotnet/api/system.threading.threadpool.setminthreads)  

## <a name="unbounded-parallelism"></a>Paralelismo ilimitado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado com o uso do paralelismo ilimitado, o que significa que não há limite imposto sobre o número de fios ou pedidos paralelos. Certifique-se de limitar pedidos paralelos para fazer upload ou descarregamento de dados, para aceder a várias divisórias na mesma conta de armazenamento ou para aceder a vários itens na mesma divisória. Se o paralelismo não estiver limitado, a sua aplicação pode exceder as capacidades do dispositivo cliente ou os alvos de escalabilidade da conta de armazenamento, resultando em tardios mais longos e estrangulamentos.  

## <a name="client-libraries-and-tools"></a>Bibliotecas e ferramentas de clientes

Para um melhor desempenho, utilize sempre as mais recentes bibliotecas e ferramentas de clientes fornecidas pela Microsoft. As bibliotecas de clientes do Azure Storage estão disponíveis para uma variedade de idiomas. O Azure Storage também suporta a PowerShell e o Azure CLI. A Microsoft desenvolve ativamente estas bibliotecas e ferramentas de clientes com desempenho em mente, mantém-nas atualizadas com as versões de serviço mais recentes, e garante que lidam com muitas das práticas de desempenho comprovadas internamente. Para mais informações, consulte a documentação de referência do [Armazenamento Azure.](/azure/storage/#reference)

## <a name="handle-service-errors"></a>Lidar com erros de serviço

O Armazenamento Azure devolve um erro quando o serviço não pode processar um pedido. Compreender os erros que podem ser devolvidos pelo Azure Storage num determinado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Timeout e Servidor Erros ocupados

O Armazenamento Azure pode acelerar a sua aplicação se se aproximar dos limites de escalabilidade. Em alguns casos, o Armazenamento Azure pode não conseguir lidar com um pedido devido a alguma condição transitória. Em ambos os casos, o serviço pode devolver um erro de 503 (Server Busy) ou 500 (Timeout). Estes erros também podem ocorrer se o serviço estiver a reequilibrar as divisórias de dados para permitir uma maior entrada. A aplicação do cliente deve normalmente voltar a tentar a operação que causa um destes erros. No entanto, se o Armazenamento Azure estiver a estrangular a sua aplicação porque está a exceder os objetivos de escalabilidade, ou mesmo se o serviço não foi capaz de servir o pedido por outra razão, as tentativas agressivas podem piorar o problema. Recomenda-se a utilização de uma política exponencial de back off de retry, e as bibliotecas dos clientes não se adenam a este comportamento. Por exemplo, a sua aplicação pode voltar a tentar após 2 segundos, depois 4 segundos, depois 10 segundos, depois 30 segundos, e depois desistir completamente. Desta forma, a sua aplicação reduz significativamente a sua carga no serviço, em vez de exacerbar comportamentos que podem levar a estrangulamentos.  

Os erros de conectividade podem ser reexperimentados imediatamente, porque não são o resultado da aceleração e espera-se que sejam transitórios.  

### <a name="non-retryable-errors"></a>Erros não retáveis

As bibliotecas dos clientes lidam com repetições com uma consciência de quais os erros que podem ser novamente julgados e que não podem. No entanto, se estiver a chamar diretamente a API de Rest de Armazenamento Azure, existem alguns erros que não deve voltar a tentar. Por exemplo, um erro de 400 (Pedido De Mau Pedido) indica que a aplicação do cliente enviou um pedido que não podia ser processado por não estar na forma esperada. A reenvio deste pedido resulta sempre na mesma resposta, pelo que não faz sentido reejá-lo. Se estiver a ligar diretamente para a API de Depósito Azure, esteja ciente de potenciais erros e se devem ser novamente julgados.

Para obter mais informações sobre os códigos de erro do Armazenamento Azure, consulte [códigos de estado e erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagle"></a>Desativar Nagle

O algoritmo de Nagle é amplamente implementado através das redes TCP/IP como um meio para melhorar o desempenho da rede. No entanto, não é o ideal em todas as circunstâncias (como ambientes altamente interativos). O algoritmo de Nagle tem um impacto negativo no desempenho dos pedidos para o serviço De Mesa Azure, e deve desativá-lo se possível.

## <a name="message-size"></a>Tamanho da mensagem

O desempenho da fila e a escalabilidade diminuem à medida que o tamanho da mensagem aumenta. Coloque apenas a informação de que o recetor necessita numa mensagem.  

## <a name="batch-retrieval"></a>Recuperação de lotes

Pode recuperar até 32 mensagens de uma fila numa única operação. A recuperação de lotes pode reduzir o número de viagens de ida e volta da aplicação do cliente, o que é especialmente útil para ambientes, como dispositivos móveis, com elevada latência.  

## <a name="queue-polling-interval"></a>Intervalo de sondagens de fila

A maioria das aplicações pesquisa maquete para mensagens de uma fila, que pode ser uma das maiores fontes de transações para essa aplicação. Selecione o intervalo de sondagens com sensatez: as sondagens com demasiada frequência podem fazer com que a sua aplicação se aproxime dos alvos de escalabilidade para a fila. No entanto, em 200.000 transações por $0,01 (no momento da escrita), uma única sondagem de processador uma vez por segundo por um mês custaria menos de 15 cêntimos, pelo que o custo não é tipicamente um fator que afeta a sua escolha de intervalo de sondagens.  

Para obter informações sobre custos atualizados, consulte o [Preço de Armazenamento Do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Utilizar mensagem de atualização

Pode utilizar a operação **Atualização de Mensagens** para aumentar o tempo de invisibilidade ou para atualizar as informações estatais de uma mensagem. Usar a Mensagem de **Atualização** pode ser uma abordagem mais eficiente do que ter um fluxo de trabalho que passa um trabalho de uma fila para a outra, uma vez que cada passo do trabalho está concluído. A sua candidatura pode salvar o estado de trabalho da mensagem e, em seguida, continuar a trabalhar, em vez de resguardar a mensagem para o próximo passo do trabalho sempre que um passo completa. Tenha em mente que cada operação de Atualização de **Mensagens** conta para o alvo de escalabilidade.

## <a name="application-architecture"></a>Arquitetura da aplicação

Use filas para tornar a arquitetura da sua aplicação escalável. As seguintes listas de algumas formas de utilizar as filas para tornar a sua aplicação mais escalável:  

- Pode utilizar filas para criar atrasos de trabalho para processamento e suavizar as cargas de trabalho na sua aplicação. Por exemplo, pode fazer fila de pedidos dos utilizadores para realizar trabalhos intensivos de processador, como redimensionar imagens carregadas.
- Pode utilizar filas para separar partes da sua aplicação para que possa dimensioná-las de forma independente. Por exemplo, uma extremidade frontal web poderia colocar os resultados do inquérito dos utilizadores numa fila para posterior análise e armazenamento. Pode adicionar mais instâncias de função de trabalhador para processar os dados da fila conforme necessário.  

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento de fila](scalability-targets.md)
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Códigos de estado e erro](/rest/api/storageservices/Status-and-Error-Codes2)

---
title: Azure Storage desempenho e escalabilidade lista de verificação | Documentos da Microsoft
description: Uma lista de verificação de práticas comprovadas para utilização com o armazenamento do Azure no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 1b6c8b1af00c2819632c60a27d61d7cf8db44885
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012326"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista de Verificação de Desempenho e Escalabilidade do Armazenamento do Microsoft Azure
## <a name="overview"></a>Descrição geral
Desde o lançamento dos serviços de armazenamento do Microsoft Azure, a Microsoft desenvolveu um número de práticas comprovadas para utilizar estes serviços num modo de alto desempenho, e este artigo serve para consolidar o mais importante numa lista de estilo de lista de verificação. A intenção deste artigo é para o ajudar a desenvolvedores de aplicativos, certifique-se de que estão a utilizar as práticas comprovadas com armazenamento do Azure e para os ajudar a identificar outras práticas comprovadas que devem considerar adotar. Este artigo não tenta abordar todos os possíveis otimização de desempenho e escalabilidade — ela exclui as que são pequenos em seu impacto ou não amplamente aplicável. Na medida em que o comportamento do aplicativo pode ser previsto durante o design, é útil para tenha isso em mente, desde o início para evitar designs que serão executadas nos problemas de desempenho.  

Todos os desenvolvedores de aplicativos com o armazenamento do Azure devem separar um tempo para ler este artigo e verificar que seu aplicativo segue cada uma das práticas comprovadas, listadas abaixo.  

## <a name="checklist"></a>Lista de verificação
Este artigo organiza as práticas comprovadas para os seguintes grupos. Práticas comprovadas aplicáveis para:  

* Todos os serviços de armazenamento do Azure (blobs, tabelas, filas e ficheiros)
* Blobs
* Tabelas
* Filas  

| Concluído | Área | Categoria | Pergunta |
| --- | --- | --- | --- |
| &nbsp; | Todos os Serviços |Destinos de escalabilidade |[A aplicação foi criada para evitar a aproximar-se os destinos de escalabilidade?](#subheading1) |
| &nbsp; | Todos os Serviços |Destinos de escalabilidade |[A Convenção de nomenclatura foi concebida para permitir a melhor balanceamento de carga?](#subheading47) |
| &nbsp; | Todos os Serviços |Redes |[Dispositivos de lado do cliente tem largura de banda suficientemente alta e baixa latência para alcançar o desempenho que é necessário?](#subheading2) |
| &nbsp; | Todos os Serviços |Redes |[Dispositivos de lado do cliente têm uma ligação de qualidade alto o suficiente?](#subheading3) |
| &nbsp; | Todos os Serviços |Redes |[A aplicação cliente encontram-se "junto" a conta de armazenamento?](#subheading4) |
| &nbsp; | Todos os Serviços |Distribuição de Conteúdos |[Está a utilizar uma CDN para distribuição de conteúdos?](#subheading5) |
| &nbsp; | Todos os Serviços |Acesso de cliente direto |[Está a utilizar SAS e o CORS para permitir o acesso direto ao armazenamento em vez de proxy?](#subheading6) |
| &nbsp; | Todos os Serviços |Colocação em cache |[Raramente é sua colocação em cache dados da aplicação que são usados repetidamente e as alterações?](#subheading7) |
| &nbsp; | Todos os Serviços |Colocação em cache |[Seu aplicativo é processamento em lote atualizações (do lado do cliente de cache-los e, em seguida, a ser carregados em conjuntos de maiores)?](#subheading8) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Já configurou seu cliente para utilizar um número suficiente de ligações simultâneas](#subheading9) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Tiver configurado o .NET para utilizar um número suficiente de threads?](#subheading10) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Está a utilizar o .NET 4.5 ou posterior, que melhorou a coleta de lixo?](#subheading11) |
| &nbsp; | Todos os Serviços |Paralelismo |[Certificar-se de que o paralelismo está determinado adequadamente, de modo que não sobrecarrega os seus recursos de cliente ou os destinos de escalabilidade?](#subheading12) |
| &nbsp; | Todos os Serviços |Ferramentas |[É que utilizar a versão mais recente do Microsoft forneceu ferramentas e bibliotecas de cliente?](#subheading13) |
| &nbsp; | Todos os Serviços |Tentativas |[Estão a utilizar um término exponencial política para a limitação de erros e tempos limite de repetição?](#subheading14) |
| &nbsp; | Todos os Serviços |Tentativas |[Tentativas de evitar seu aplicativo destina-se a erros de não repetição?](#subheading15) |
| &nbsp; | Blobs |Destinos de escalabilidade |[Tem um grande número de clientes acessando um único objeto ao mesmo tempo?](#subheading46) |
| &nbsp; | Blobs |Destinos de escalabilidade |[Seu aplicativo estiver acordado dentro da meta de escalabilidade da largura de banda ou operações para um único blob?](#subheading16) |
| &nbsp; | Blobs |Copiar Blobs |[São blobs copiar de uma forma eficiente?](#subheading17) |
| &nbsp; | Blobs |Copiar Blobs |[Está a utilizar o AzCopy para cópia em massa de blobs?](#subheading18) |
| &nbsp; | Blobs |Copiar Blobs |[Está a utilizar Importar/exportar do Azure para transferir grandes volumes de dados?](#subheading19) |
| &nbsp; | Blobs |Utilizar metadados |[São armazenar metadados usados com freqüência sobre blobs nos respetivos metadados?](#subheading20) |
| &nbsp; | Blobs |Carregamento rápido |[Ao tentar carregar um blob rapidamente, está carregando blocos em paralelo?](#subheading21) |
| &nbsp; | Blobs |Carregamento rápido |[Ao tentar carregar os blobs rapidamente, está carregando blobs em paralelo?](#subheading22) |
| &nbsp; | Blobs |Tipo de Blob correto |[Está usando blobs de páginas ou blobs de blocos, quando apropriado?](#subheading23) |
| &nbsp; | Tabelas |Destinos de escalabilidade |[A aproximar-nos destinos de escalabilidade para entidades por segundo?](#subheading24) |
| &nbsp; | Tabelas |Configuração |[Está usando JSON para os pedidos de tabela?](#subheading25) |
| &nbsp; | Tabelas |Configuração |[Desativou a Nagle para melhorar o desempenho de pequenos pedidos?](#subheading26) |
| &nbsp; | Tabelas |Tabelas e partições |[Ter corretamente partições dos seus dados?](#subheading27) |
| &nbsp; | Tabelas |Partições muito ativas |[Evitando só de acréscimo e só de preceder padrões?](#subheading28) |
| &nbsp; | Tabelas |Partições muito ativas |[As inserções/atualizações são distribuídas por várias partições?](#subheading29) |
| &nbsp; | Tabelas |Âmbito de consulta |[Projetado seu esquema para permitir consultas de ponto a ser usado na maioria dos casos e consultas de tabela para ser utilizadas com moderação?](#subheading30) |
| &nbsp; | Tabelas |Densidade de consulta |[Fazer o exame normalmente apenas consultas e devolvem linhas que irá utilizar a sua aplicação?](#subheading31) |
| &nbsp; | Tabelas |Limitar os dados devolvidos |[Está a utilizar filtragem para evitar a retornar entidades que não são necessários?](#subheading32) |
| &nbsp; | Tabelas |Limitar os dados devolvidos |[Está a utilizar projeção para evitar a retornar as propriedades que não são necessários?](#subheading33) |
| &nbsp; | Tabelas |Desnormalização |[Ter desnormalizado seus dados, de modo que a evitar o consultas ineficientes ou várias solicitações de leitura ao tentar obter dados?](#subheading34) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Está a criação de batches pedidos que precisem de ser transacional ou pode ser feito ao mesmo tempo para reduzir viagens?](#subheading35) |
| &nbsp; | Tabelas |Insert/Update/Delete |[São evitando a obter uma entidade apenas para determinar se a chamada de inserção ou atualização?](#subheading36) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Tenha considerado armazenando série de dados que serão recuperados com frequência em conjunto numa única entidade como propriedades em vez de várias entidades?](#subheading37) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Para entidades que serão sempre obtidas em conjunto e podem ser escritas em lotes (por exemplo, dados de séries de tempo), consideraram usando blobs em vez de tabelas?](#subheading38) |
| &nbsp; | Filas |Destinos de escalabilidade |[A aproximar-nos destinos de escalabilidade para mensagens por segundo?](#subheading39) |
| &nbsp; | Filas |Configuração |[Desativou a Nagle para melhorar o desempenho de pequenos pedidos?](#subheading40) |
| &nbsp; | Filas |Tamanho da Mensagem |[São o compact de mensagens para melhorar o desempenho da fila?](#subheading41) |
| &nbsp; | Filas |Recuperar em massa |[São obter várias mensagens numa única operação "Obter"?](#subheading42) |
| &nbsp; | Filas |Frequência de consulta |[É consulta com tanta freqüência para reduzir a latência percebida da sua aplicação?](#subheading43) |
| &nbsp; | Filas |Atualizar mensagem |[Está a utilizar UpdateMessage para armazenar o progresso no processamento de mensagens, evitando Reprocessar a mensagem inteira, se ocorrer um erro?](#subheading44) |
| &nbsp; | Filas |Arquitetura |[Está a utilizar filas para fazer com que todo o seu aplicativo mais escalonável ao manter as cargas de trabalho de longa execução fora do caminho crítico e dimensionar de forma independente, em seguida?](#subheading45) |

## <a name="allservices"></a>Todos os serviços
Esta secção lista as práticas comprovadas que são aplicáveis à utilização de qualquer um dos serviços de armazenamento do Azure (blobs, tabelas, filas ou ficheiros).  

### <a name="subheading1"></a>Destinos de escalabilidade
O armazenamento do Azure em si tem um limite de 250 contas de armazenamento por região por subscrição. Se atingir esse limite, não será possível criar as contas de armazenamento mais nessa combinação de subscrição/região.

Cada um dos serviços de armazenamento do Azure tem destinos de escalabilidade de capacidade (GB), a taxa de transação e largura de banda. Se seu aplicativo atinja ou exceda qualquer um dos destinos de escalabilidade, pode encontrar as latências de transação de aumento ou a limitação. Quando um serviço de armazenamento regula a sua aplicação, o serviço começa a retornar "503 servidor ocupado" ou "500 tempo limite da operação" códigos de erro para algumas transações de armazenamento. Esta secção descreve a abordagem geral para lidar com destinos de escalabilidade e destinos de escalabilidade de largura de banda em particular. As secções que lidam com os serviços de armazenamento individuais discutem destinos de escalabilidade no contexto desse serviço específico:  

* [Largura de banda do blob e de pedidos por segundo](#subheading16)
* [Entidades da tabela por segundo](#subheading24)
* [Mensagens de fila por segundo](#subheading39)  

#### <a name="sub1bandwidth"></a>Destino de escalabilidade de largura de banda para todos os serviços
No momento da escrita, os destinos de largura de banda nos Estados Unidos para uma conta de armazenamento georredundante (GRS) são 10 gigabits por segundo (Gbps) para a entrada (dados enviados para a conta de armazenamento) e 20 Gbps para saída (dados enviados a partir da conta de armazenamento). Para uma conta de armazenamento localmente redundante (LRS), os limites são superiores – 20 Gbps para entrada e de 30 Gbps para saída.  Limites de largura de banda internacional pode ser mais baixos e pode ser encontrados no nosso [página de destinos de escalabilidade](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Para obter mais informações sobre as opções de redundância de armazenamento, veja as ligações na útil recursos a seguir.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>O que fazer quando a atingir uma meta de escalabilidade
Se estiver a atingir o limite de contas de armazenamento, que pode ter numa combinação de subscrição/região em particular, avaliar o aplicativo e a utilização de contas de armazenamento e determinar se alguma das seguintes condições se aplicam.

* Utilizar contas de armazenamento como discos não geridos e adicionar esses discos para as máquinas virtuais. Neste cenário, recomendamos que utilize [discos geridos](../../virtual-machines/windows/managed-disks-overview.md), como eles manipulam escalabilidade de disco de armazenamento para, sem ter de voltar a criar e gerir contas de armazenamento individuais.
* Utilizar uma conta de armazenamento numa base por cliente, para efeitos de isolamento de dados. Neste cenário, recomendamos que utilize contentores de armazenamento para cada cliente, em vez de uma conta de armazenamento completo. O armazenamento do Azure agora permite-lhe especificar o controlo de acesso baseado em funções num por [base de contentor](storage-auth-aad-rbac.md).
* Utilizar várias contas de armazenamento para partições horizontais para maior escalabilidade de entrada/saída/iops/capacidade. Neste cenário, se possível, recomendamos que aproveitar o [aumentou limites](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) de contas de armazenamento standard para reduzir o número de contas de armazenamento necessário para a sua carga de trabalho.

Se seu aplicativo está a atingir as metas de escalabilidade para uma única conta de armazenamento, considere adotar uma das abordagens seguintes:  

* Reconsidere a carga de trabalho que faz com que seu aplicativo abordar ou exceder o destino de escalabilidade. Pode desenhá-lo de forma diferente para utilizar menos largura de banda ou capacidade ou menos transações?
* Se um aplicativo pode exceder um dos destinos de escalabilidade, deve criar várias contas de armazenamento e a partição de dados da sua aplicação nessas várias contas de armazenamento. Se usar esse padrão, não se esqueça de criar seu aplicativo para que pode adicionar mais contas de armazenamento no futuro para balanceamento de carga. No momento da escrita, cada subscrição do Azure pode ter até 100 contas de armazenamento.  Contas de armazenamento também têm sem custos que não seja a sua utilização em termos de dados armazenados, transações feitas ou dados transferidos.
* Se o aplicativo acessa os destinos de largura de banda, considere a compressão de dados no cliente para reduzir a largura de banda necessária para enviar os dados para o serviço de armazenamento.  Embora isto pode poupar largura de banda e melhorar o desempenho de rede, também pode ter alguns impactos negativos.  Deve avaliar o impacto no desempenho isso por conta dos requisitos de processamento adicional para compactando e descompactando dados no cliente. Além disso, armazenar dados comprimidos pode dificultar a resolução de problemas, uma vez que poderia ser mais difícil ver os dados armazenados usando as ferramentas padrão.
* Se o aplicativo acessa os destinos de escalabilidade, em seguida, certifique-se de que está a utilizar um término exponencial para repetições (consulte [repetições](#subheading14)).  É melhor para se certificar-se de que nunca aborda os destinos de escalabilidade (ao utilizar um dos métodos acima), mas isto irá garantir que a sua aplicação não apenas continuará a tentar rapidamente, tornando a limitação pior.  

#### <a name="useful-resources"></a>Recursos Úteis
As ligações seguintes fornecem detalhes adicionais sobre destinos de escalabilidade:

* Ver [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter informações sobre metas de escalabilidade.
* Ver [replicação de armazenamento do Azure](storage-redundancy.md) e a mensagem de blogue [opções de redundância de armazenamento do Azure e o acesso de leitura armazenamento Georredundante](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) para obter informações sobre as opções de redundância de armazenamento.
* Para obter informações atualizadas sobre os preços para os serviços do Azure, consulte [os preços do Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Convenção de nomenclatura de partições
Armazenamento do Azure utiliza um esquema de particionamento baseado em intervalo para balanceamento de carga e dimensionamento do sistema. A chave de partição é utilizada para a partição de dados em intervalos e estes intervalos são com balanceamento de carga em todo o sistema. Isso significa que as convenções de nomenclatura, como a ordem léxica (por exemplo, msftpayroll, msftperformance, msftemployees, etc.) ou com carimbos tempo (log20160101, log20160102, log20160102, etc.) serão aberto para as partições que está a ser potencialmente colocalizadas na mesmo servidor de partições, até que uma operação de balanceamento de carga divide-los por em intervalos mais pequenos. Por exemplo, todos os blobs num contentor podem ser atendidos por um único servidor até que a carga nestes BLOBs requer mais reequilibrar dos intervalos de partição. Da mesma forma, um grupo de contas apenas superficialmente carregá-lo com seus nomes organizados em ordem léxica pode ser servido por um único servidor até a carga em uma ou todas estas contas requerem-los para ser divididas em vários servidores de partições. Cada operação de balanceamento de carga pode afetar a latência de chamadas de armazenamento durante a operação. A capacidade do sistema para processar uma rajada repentina de tráfego para uma partição é limitada pela escalabilidade de um servidor de partição única até que a operação de balanceamento de carga no entra em ação e rebalances o intervalo da chave de partição.  

Pode seguir algumas práticas recomendadas para reduzir a frequência destas operações.  

* Examine a Convenção de nomenclatura que utiliza para contas, contentores, blobs, tabelas e filas, de perto. Considere prefixando nomes de conta com um hash de 3 dígitos a utilizar uma função de hash mais adequado às suas necessidades.  
* Se organizar os seus dados com carimbos de data / ou identificadores numéricos, terá de garantir que não estiver a utilizar um padrões de tráfego só de acréscimo (ou apenas de preceder). Estes padrões não são adequados para um intervalo de-com base em sistema de criação de partições, e foi líder para todo o tráfego vai para uma única partição e a limitação de efetivamente o sistema de balanceamento de carga. Por exemplo, se tiver as operações diárias do que usar um objeto de blob com um carimbo como AAAAMMDD, em seguida, todo o tráfego para essa operação diária é direcionado para um único objeto, o que é servido por um servidor de partição única. Verificar se o por limites de blob por partição limites de atender às suas necessidades e considere a divisão esta operação em vários blobs, se necessário. Da mesma forma, se armazenar dados de séries de tempo das tabelas, todo o tráfego pode ser direcionado para a última parte do espaço de nomes principais. Se tiver de utilizar carimbos ou IDs numéricas, o prefixo do ID com um hash de 3 dígitos ou, no caso de carimbos de data / prefixo a parte de segundos do tempo, como ssyyyymmdd. Se a listagem e nas operações de consulta são executadas rotineiramente, escolha uma função de hash que irá limitar o número de consultas. Em outros casos, um prefixo aleatório pode ser suficiente.  
* Para obter mais informações sobre o esquema de particionamento usado no armazenamento do Azure, leia o artigo SOSP [aqui](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Redes
Embora a API chame questão, muitas vezes, as restrições de rede física do aplicativo tem um impacto significativo no desempenho. A seguir descreve algumas das limitações que os utilizadores poderão encontrar.  

#### <a name="client-network-capability"></a>Capacidade de rede do cliente
##### <a name="subheading2"></a>Taxa de transferência
Largura de banda, o problema é, muitas vezes, as capacidades do cliente. Por exemplo, enquanto uma única conta de armazenamento pode processar 10 Gbps ou mais de entrada (consulte [destinos de escalabilidade de largura de banda](#sub1bandwidth)), a velocidade da rede numa instância de função de trabalho do Azure "Small" só é capaz de aproximadamente 100 Mbps. As instâncias do Azure maiores tem NICs com uma capacidade maior, pelo que deve considerar utilizar uma instância maior ou mais VMs, se precisar de limites de rede mais de uma única máquina. Se estiver a aceder um serviço de armazenamento de uma aplicação de local no, então a mesma regra se aplica: compreender as capacidades de rede do dispositivo cliente e a conectividade de rede para a localização de armazenamento do Azure e qualquer um aperfeiçoá-los conforme necessário ou estruturar seu aplicação funcione dentro de seus recursos.  

##### <a name="subheading3"></a>Qualidade de ligação
Tal como acontece com qualquer utilização de rede, lembre-se de que as condições de rede resulta em erros e perda de pacotes também haverá um abrandamento taxa de transferência efetiva.  Com o WireShark ou NetMon pode ajudar a diagnosticar este problema.  

##### <a name="useful-resources"></a>Recursos Úteis
Para obter mais informações sobre tamanhos de máquinas virtuais e a largura de banda alocada, consulte [tamanhos de VM do Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [tamanhos de VM do Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>localização
Em qualquer ambiente distribuído, colocar o cliente próximo servidor apresenta no melhor desempenho. Para aceder ao armazenamento do Azure com a menor latência, o melhor local para o cliente está na mesma região do Azure. Por exemplo, se tiver um Web Site do Azure que utiliza o armazenamento do Azure, deve localizar ambos numa única região (por exemplo, e.u.a. Centro-Oeste ou Sudeste da Ásia). Isso reduz a latência e o custo — no momento da escrita, a utilização de largura de banda numa única região é gratuita.  

Se o seu cliente de aplicativos não são hospedados no Azure (como aplicações de dispositivos móveis ou nos serviços de empresa no local), em seguida, novamente a colocar a conta de armazenamento numa região próximo os dispositivos que irão aceder aos mesmos, geralmente irá reduzir latência. Se os clientes são amplamente distribuídos (por exemplo, alguns na América do Norte e alguns na Europa), então deve considerar a utilização de várias contas de armazenamento: uma localizados numa região da América do Norte e um numa região da Europa. Isto irá ajudar a reduzir a latência para os utilizadores em ambas as regiões. Essa abordagem é mais fácil de implementar, se a aplicação armazena os dados é específico para utilizadores individuais e não necessita de replicar dados entre contas de armazenamento.  Para ampla distribuição de conteúdo, recomenda-se uma CDN – consulte a secção seguinte para obter mais detalhes.  

### <a name="subheading5"></a>Distribuição de conteúdo
Às vezes, um aplicativo precisa servir o mesmo conteúdo a vários usuários (por exemplo, um produto vídeo de demonstração utilizado na home page de um Web site), localizado no mesmo ou várias regiões. Neste cenário, deve utilizar uma rede de entrega de conteúdos (CDN), como a CDN do Azure e CDN usaria o armazenamento do Azure como a origem dos dados. Ao contrário de uma conta de armazenamento do Azure que existe numa única região e que não é possível fornecer o conteúdo com baixa latência para outras regiões, a CDN do Azure utiliza servidores em vários datacenters em todo o mundo. Além disso, uma CDN, normalmente, pode suportar limites de saída muito mais do que uma conta de armazenamento única.  

Para obter mais informações sobre a CDN do Azure, consulte [CDN do Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Com o SAS e o CORS
Quando precisar de autorizar o código, como o JavaScript no navegador da web de um utilizador ou uma aplicação de telemóvel para aceder a dados no armazenamento do Azure, uma abordagem é usar um aplicativo na função da web como um proxy: o dispositivo do utilizador é autenticado com a função da web , que por sua vez autoriza o acesso aos recursos de armazenamento. Dessa forma, pode evitar expor as chaves de conta de armazenamento em dispositivos não seguro. No entanto, isso coloca uma sobrecarga significativa na função da web porque todos os dados transferidos entre o dispositivo do utilizador e o serviço de armazenamento tem de passar através da função da web. Pode evitar com uma função da web como um proxy para o serviço de armazenamento ao utilizar a partilhado assinaturas de acesso (SAS), às vezes, juntamente com cabeçalhos de partilha de recursos de várias origens (CORS). Através da SAS, pode permitir que o dispositivo do utilizador fazer pedidos diretamente para um serviço de armazenamento por meio de um token de acesso limitado. Por exemplo, se um usuário deseja carregar uma fotografia para a sua aplicação, a função da web pode gerar e enviar para o dispositivo do utilizador, um token SAS que concede permissão para escrever um blob específico ou contentor nos próximos 30 minutos (após o qual o token SAS expira).

Normalmente, um browser não permitirá que JavaScript numa página hospedada por um Web site num domínio para fazer operações específicas, como um "Colocar" para outro domínio. Por exemplo, se aloja uma função da web em "contosomarketing.cloudapp.net" e desejar usar o JavaScript do lado do cliente para carregar um blob para a sua conta de armazenamento em "contosoproducts.blob.core.windows.net", os navegadores "diretiva de mesma origem" serão proibir esta operação. CORS é uma funcionalidade de navegador que permite que o domínio de destino (no caso a conta de armazenamento) para comunicar com o navegador que confia pedidos com origem no domínio de origem (no caso a função da web).  

Essas tecnologias podem ajudar a evitar uma carga desnecessária (e afunilamentos) na sua aplicação web.  

#### <a name="useful-resources"></a>Recursos Úteis
Para obter mais informações sobre SAS, consulte [assinaturas de acesso partilhado, parte 1: Compreender o modelo SAS](../storage-dotnet-shared-access-signature-part-1.md).  

Para obter mais informações sobre o CORS, consulte [suporte de Cross-Origin Resource Sharing (CORS) para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Colocação em cache
#### <a name="subheading7"></a>Obtenção de dados
Em geral, a obtenção de dados de um serviço, uma vez é melhor do que fazê-lo duas vezes. Considere o exemplo de uma aplicação de web MVC em execução numa função da web que já obtiver um blob de 50 MB do serviço de armazenamento para servir de conteúdo a um utilizador. A aplicação, em seguida, pode obter esse mesmo blob sempre que um utilizador solicita-lo ou ele pode colocar em cache localmente para o disco e reutilizar a versão em cache para pedidos de utilizador subsequentes. Além disso, sempre que um utilizador solicita os dados, o aplicativo foi possível problema obter com um cabeçalho condicional para hora de modificação, o que seria evitar a que o blob inteiro se este não foi modificado. Pode aplicar esse mesmo padrão para trabalhar com entidades da tabela.  

Em alguns casos, pode decidir que seu aplicativo pode assumir que o blob permanece válido durante um curto período após recuperá-lo e que durante este período o aplicativo não precisa verificar se o blob foi modificado.

Configuração, pesquisa e outros dados que são sempre utilizados pela aplicação são excelentes candidatas para a colocação em cache.  

Para obter um exemplo de como obter as propriedades de um blob para descobrir a data da última modificação usando o .NET, consulte [conjunto e obter propriedades e metadados](../blobs/storage-properties-metadata.md). Para obter mais informações sobre downloads condicionais, consulte [condicionalmente atualizar uma cópia Local de um Blob](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Carregamento de dados em lotes
Em alguns cenários de aplicação, pode agregar dados localmente e, em seguida, carregá-lo periodicamente num lote, em vez de carregamento de cada um dos dados imediatamente. Por exemplo, um aplicativo web pode manter um ficheiro de registo de atividades: a aplicação poderia carregar detalhes de cada atividade à medida que ele acontece como uma entidade de tabela (que requer muitas operações de armazenamento) ou poderia economizar detalhes da atividade num arquivo de log local e, em seguida, Carregue periodicamente todos os detalhes da atividade como um ficheiro delimitado para um blob. Se cada entrada de log é 1 KB de tamanho, pode carregar milhares numa única transação "Colocar o Blob" (pode carregar um blob de até 64 MB de tamanho numa única transação). No caso de falha no computador local antes do carregamento, potencialmente irá perder alguns dados de registo: o programador da aplicação tem de criar para a possibilidade de dispositivo cliente ou falhas de carregamento.  Se os dados de atividade têm de ser transferidos para períodos de tempo (não apenas única atividade), são recomendadas blobs através de tabelas.

### <a name="net-configuration"></a>Configuração do .NET
Se utilizar o .NET Framework, esta secção lista várias definições de configuração rápida que pode usar para fazer melhorias de desempenho significativas.  Se utilizar outros idiomas, verifique se a conceitos similares aplicam-se na linguagem que escolheu.  

#### <a name="subheading9"></a>Aumentar o limite de ligações padrão
No .NET, o código a seguir, aumenta o predefinição limite de ligações (que normalmente é 2 num ambiente cliente ou 10 num ambiente de servidor) a 100. Normalmente, deve definir o valor para aproximadamente o número de threads utilizado pela sua aplicação.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Tem de definir o limite de ligação antes de abrir todas as ligações.  

Para outras linguagens de programação, consulte a documentação desse idioma para determinar como definir o limite de ligação.  

Para obter mais informações, consulte a mensagem de blogue [serviços da Web: Ligações simultâneas](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Aumentar Threads do ThreadPool Min, se utilizar o código síncrono com tarefas assíncronas
Esse código irá aumentar os threads do pool min:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, consulte [ThreadPool.SetMinThreads método](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Tire partido de coleta de lixo do .NET 4.5
Use o .NET 4.5 ou posterior para a aplicação de cliente para tirar partido das melhorias de desempenho na coleta de lixo do servidor.

Para obter mais informações, consulte o artigo [uma visão geral de melhorias de desempenho do .NET 4.5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Não vinculado de paralelismo
Embora seja ótimo para desempenho paralelismo, tenha cuidado ao utilizando o paralelismo não vinculado (sem limite no número de threads de e/ou pedidos paralelos) para carregar ou transferir dados, usando vários operadores para aceder a várias partições (contentores, filas, ou partições de tabela) na mesma conta de armazenamento ou para aceder a vários itens na mesma partição. Se o paralelismo é imensos, seu aplicativo pode ter mais de capacidades do dispositivo cliente ou escalabilidade da conta de armazenamento está direcionada a limitação e resultando em períodos de latência superiores.  

### <a name="subheading13"></a>Ferramentas e bibliotecas de cliente de armazenamento
Utilize sempre o mais recentes da Microsoft fornecida ferramentas e bibliotecas de cliente. No momento da escrita, existem bibliotecas de cliente disponíveis para .NET, o Windows Phone, o tempo de execução do Windows, o Java e o C++, bem como bibliotecas de pré-visualização para outros idiomas. Além disso, a Microsoft lançou cmdlets do PowerShell e comandos da CLI do Azure para trabalhar com o armazenamento do Azure. Microsoft ativamente desenvolve essas ferramentas com o desempenho em mente, mantém atualizadas com as versões mais recentes do serviço e garante que eles manipulam muitas das práticas de desempenho comprovados internamente.  

### <a name="retries"></a>Tentativas
#### <a name="subheading14"></a>Throttling/ServerBusy
Em alguns casos, o serviço de armazenamento pode limitar a sua aplicação ou pode ser simplesmente não é possível atender à solicitação devido a alguma condição transitória e devolver uma mensagem de "503 servidor ocupado" ou "tempo limite de 500".  Isto pode acontecer se a sua aplicação está a aproximar-se qualquer um dos destinos de escalabilidade, ou se o sistema é reequilibrar os dados particionados para permitir um débito mais elevado.  O aplicativo cliente normalmente deve repetir a operação que faz com que este tipo de erro: a tentar mais tarde do mesmo pedido terá êxito. No entanto, se o serviço de armazenamento está a limitar a aplicação porque está a exceder os destinos de escalabilidade, ou mesmo que o serviço não foi possível atender à solicitação por algum motivo, as repetições agressivas normalmente piorar o problema. Por esse motivo, deve usar um (o padrão de bibliotecas de cliente para esse comportamento) de término exponencial. Por exemplo, seu aplicativo poderá novamente depois de 2 segundos, em seguida, de 4 segundos, depois de 10 segundos depois de 30 segundos e, em seguida, abrir mão completamente. Este comportamento resulta em seu aplicativo significativamente reduzindo a carga no serviço em vez de exacerbating quaisquer problemas.  

Erros de conectividade podem ser repetidos imediatamente, pois não são o resultado da limitação e devem ser transitórios.  

#### <a name="subheading15"></a>Erros de não repetição
As bibliotecas de cliente têm conhecimento dos quais erros são capazes de repetição e quais não estão. No entanto, se estiver escrevendo seu próprio código em relação o REST API do armazenamento, lembre-se há alguns erros que não deve repetir: por exemplo, um 400 (pedido incorreto), com resposta indica que a aplicação cliente enviou um pedido que não foi possível processar porque não era num formato esperado. Reenviar este pedido fará com que a mesma resposta cada vez, portanto, não há nenhuma razão para repetir ele. Se estiver a escrever seu próprio código em relação o REST API do armazenamento, tenha em atenção o que significa que os códigos de erro e o modo adequado para tentar novamente (ou não) para cada uma delas.  

#### <a name="useful-resources"></a>Recursos Úteis
Para obter mais informações sobre códigos de erro de armazenamento, consulte [Status e códigos de erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) no site do Microsoft Azure.  

## <a name="blobs"></a>Blobs
Para além de práticas comprovadas para [todos os serviços](#allservices) descrito anteriormente, as seguintes práticas comprovadas aplicam-se especificamente ao serviço de Blobs.  

### <a name="blob-specific-scalability-targets"></a>Destinos de escalabilidade de blob específicos
#### <a name="subheading46"></a>Vários clientes em simultâneo a aceder um único objeto
Se tiver um grande número de clientes acessando um único objeto ao mesmo tempo, terá de considerar por objeto e o armazenamento de destinos de escalabilidade da conta. O número exato de clientes que podem aceder a um único objeto irá variar dependendo de fatores como o número de clientes que solicitem o objeto ao mesmo tempo, o tamanho do objeto, rede condições etc.

Se o objeto pode ser distribuído por meio de uma CDN, como imagens ou vídeos atendidos a partir de um Web site, em seguida, deve usar uma CDN. Ver [aqui](#subheading5).

Em outros cenários, tais como simulações científicos, onde os dados são confidenciais, tem duas opções. O primeiro é escalonar acesso da sua carga de trabalho de forma a que o objeto é acessado durante um período de tempo vs a ser acedidos em simultâneo. Em alternativa, pode copiar temporariamente o objeto para várias contas de armazenamento, aumentando assim o IOPS total por objeto e, em contas de armazenamento. No teste limitado, Descobrimos que aproximadamente 25 VMs em simultâneo foi possível transferir um blob de 100 GB em paralelo (cada VM foi paralelizar o download usando 32 segmentos). Se tivesse 100 clientes que precisem de aceder ao objeto, primeiro copie-o para uma segunda conta de armazenamento e, em seguida, tem as 50 primeiras VMs acessar o blob primeiro e o acesso de VMs em segundo lugar 50 o blob de segundo. Os resultados variam consoante o comportamento de aplicativos para que deve testar isso durante o design. 

#### <a name="subheading16"></a>Largura de banda e de operações por Blob
Pode ler ou gravar num único blob em até um máximo de 60 MB por segundo (isto é aproximadamente de 480 Mbps, que excede os recursos de várias redes de lado do cliente (incluindo o NIC físico no dispositivo cliente). Além disso, um único blob suporta até 500 pedidos por segundo. Se tiver vários clientes que precisam de ler o mesmo blob e poderá exceder estes limites, deve considerar a utilização de uma CDN para a distribuição de blob.  

Para obter mais informações sobre o débito de destino para blobs, veja [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copiar e mover Blobs
#### <a name="subheading17"></a>Copiar Blob
A versão 2012-02-12 de API de REST do armazenamento introduziu a capacidade útil de copiar blobs em contas: uma aplicação cliente pode instruir o serviço de armazenamento para copiar um blob a partir de outra origem (possivelmente sob uma conta de armazenamento diferente) e, em seguida, permitem executar o serviço a cópia de forma assíncrona. Isso pode reduzir significativamente a largura de banda necessária para a aplicação quando estiver a migrar dados de outras contas de armazenamento porque não é necessário transferir e carregar os dados.  

Uma consideração, no entanto, é que, ao copiar entre contas de armazenamento, não há nenhuma garantia de vez em quando a cópia será concluída. Se a sua aplicação precisa para concluir uma cópia do blob rapidamente em seu controle, poderá ser melhor copiar o blob ao transferi-lo a uma VM e, em seguida, carregá-lo para o destino.  Total previsibilidade nessa situação, certifique-se de que a cópia é realizada por uma VM em execução na mesma região do Azure, caso contrário, condições de rede pode (e provavelmente será) afetam o desempenho da sua cópia.  Além disso, pode monitorizar o progresso de uma cópia assíncrona por meio de programação.  

Cópias dentro da mesma conta de armazenamento em si geralmente são concluídas rapidamente.  

Para obter mais informações, consulte [Blob de cópia](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Utilizar o AzCopy
A equipa do armazenamento do Azure lançou uma ferramenta da linha de comandos "AzCopy" que é criada para transferir os blobs para, de e em contas de armazenamento de massa.  Essa ferramenta está otimizada para este cenário e pode atingir velocidades de transferência elevada.  Seu uso é incentivado para carregamento em massa, transferir e cenários de cópia. Para saber mais sobre isso e transferi-lo, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Serviço importar/exportar do Azure
Para grandes volumes de dados (mais de 1 TB), o armazenamento do Azure oferece o serviço de importação/exportação, o que permite carregar e transferir a partir do armazenamento de BLOBs por unidades de disco rígido de envio.  Pode colocar os seus dados num disco rígido e enviá-lo para a Microsoft para o carregamento ou enviar um disco rígido em branco para a Microsoft para transferir dados.  Para obter mais informações, veja [Utilizar o Serviço de Importação/Exportação do Microsoft Azure para Transferir Dados para o Armazenamento de Blobs](../storage-import-export-service.md).  Isso pode ser muito mais eficiente do que carregar/transferir este volume de dados através da rede.  

### <a name="subheading20"></a>Utilizar metadados
O serviço de BLOBs suporta pedidos principais, que podem incluir metadados sobre o blob. Por exemplo, se seu aplicativo necessário que os dados EXIF uma fotografia, pode recuperar a foto e extraí-lo. Para poupar largura de banda e melhorar o desempenho, seu aplicativo pode armazenar os dados EXIF nos metadados do blob quando o aplicativo carregado a fotografia: pode, em seguida, obter os dados EXIF nos metadados usando apenas um pedido HEAD, poupando largura de banda significativa e o tempo de processamento necessário extrair os dados EXIF sempre que o blob é de leitura. Isso poderia ser útil em cenários em que apenas tem dos metadados e não o conteúdo completo de um blob.  Apenas de 8 KB de metadados podem ser armazenado por blob (o serviço não aceitará um pedido para o armazenamento mais elevado), portanto, se os dados não cabem nesse tamanho, pode não ser capaz de usar essa abordagem.  

Para obter um exemplo de como obter os metadados de um blob através do .NET, consulte [conjunto e obter propriedades e metadados](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Carregamento rápido
Para carregar blobs rapidamente, a primeira pergunta para responder é: carregar um blob ou muitos?  Utilize as seguintes instruções para determinar o método correto a utilizar consoante o seu cenário.  

#### <a name="subheading21"></a>Carregar um blob de grandes rapidamente
Para carregar um único blob grandes rapidamente, a aplicação cliente deverá carregar os respetivos blocos ou páginas em paralelo (tocante dos destinos de escalabilidade para blobs individuais e a conta de armazenamento como um todo).  As bibliotecas de disponibilizadas pela Microsoft os clientes de armazenamento RTM oficiais (.NET, Java) têm a capacidade de fazer isso.  Para cada uma das bibliotecas, utilize o abaixo de objeto ou propriedade especificada para definir o nível de simultaneidade:  

* .NET: Definir ParallelOperationThreadCount num objeto de BlobRequestOptions a ser utilizado.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Utilize parallelOperationThreadCount sobre as opções de pedido ou o serviço de Blobs.
* C++: Use o método blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Carregar os blobs rapidamente
Para carregar rapidamente os blobs, carregar blobs em paralelo. Isso é mais rápido do que carregar blobs únicas cada vez com carregamentos de bloco paralelo, porque ele propaga o carregamento em várias partições do serviço de armazenamento. Um único blob só suporta um débito de 60 MB por segundo (aproximadamente 480 Mbps). No momento da escrita, uma conta LRS baseadas nos E.U.A. suporta um máximo de 20 Gbps entrada que é muito mais do que o débito suportado por um blob individual.  [AzCopy](#subheading18) executa carregamentos em paralelo, por predefinição e é recomendado para este cenário.  

### <a name="subheading23"></a>Escolher o tipo correto de blob
O armazenamento do Azure suporta dois tipos de BLOBs: *página* blobs e *bloco* blobs. Para um cenário de uso determinado, à sua escolha do tipo de blob irá afetar o desempenho e escalabilidade da sua solução. Os blobs de blocos são adequados para carregar grandes quantidades de dados de forma eficiente: por exemplo, um aplicativo cliente talvez seja necessário carregar fotos ou vídeo ao armazenamento de Blobs. Blobs de páginas são adequados se a aplicação tem de realizar as gravações aleatórias nos dados: por exemplo, os VHDs do Azure são armazenadas como blobs de página.  

Para obter mais informações, consulte [Noções básicas sobre Blobs de blocos, Blobs de acréscimo e Blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabelas
Para além de práticas comprovadas para [todos os serviços](#allservices) descrito anteriormente, as seguintes práticas comprovadas aplicam-se ao serviço de tabela.  

### <a name="subheading24"></a>Destinos de escalabilidade de tabela específica
Além das limitações de largura de banda de uma conta de armazenamento completo, as tabelas têm o limite de escalabilidade específico seguinte.  O sistema será balanceamento de carga à medida que aumenta tráfego, mas se o tráfego tiver picos repentinos, poderá não conseguir obter este volume de débito de imediato.  Se o seu padrão de tiver picos, deve esperar para ver a limitação de e/ou tempos limite durante o fluxo, como o armazenamento do serviço automaticamente balanceamentos de carga fora da sua tabela.  Aumentando gradualmente lentamente geralmente tem resultados melhores, como ele fornece a hora do sistema para balanceamento de carga adequadamente.  

#### <a name="entities-per-second-account"></a>Entidades por segundo (conta)
O limite de escalabilidade para aceder a tabelas é até 20 000 entidades (1 KB cada) por segundo de uma conta.  Em geral, cada entidade que é inserida, atualizado, eliminado ou analisados contagens para este destino.  Portanto, uma inserção de lote que contém 100 entidades contabilizaria como 100 entidades.  Uma consulta que verifica a existência de entidades de 1000 retorna 5 seria contar como 1000 entidades.  

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)
Dentro de uma única partição, o destino de escalabilidade para aceder a tabelas é 2.000 entidades (1 KB cada) por segundo, com a mesma contagem conforme descrito na secção anterior.  

### <a name="configuration"></a>Configuração
Esta secção lista as várias definições de configuração rápida que pode utilizar para fazer melhorias de desempenho significativo no serviço de tabela:  

#### <a name="subheading25"></a>Utilizar o JSON
A partir da versão de serviço de armazenamento 08-2013-15, o serviço de tabela suporta com JSON em vez do formato AtomPub baseado em XML para a transferência de dados da tabela. Isso pode reduzir a tamanhos de payload, até 75% e pode melhorar significativamente o desempenho da sua aplicação.

Para obter mais informações, consulte a postagem [tabelas do Microsoft Azure: Introdução ao JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) e [formato de Payload para operações de serviço de tabela](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle desativado
Algoritmo do Nagle amplamente é implementado através de redes de TCP/IP como um meio para melhorar o desempenho de rede. No entanto, não é ideal em todas as circunstâncias (por exemplo, ambientes altamente interativas). Armazenamento do Azure, algoritmo do Nagle tem um impacto negativo no desempenho de pedidos para os serviços de tabela e fila e deve desabilitá-la se possível.  

Para obter mais informações, consulte nossa mensagem de blogue [algoritmo do Nagle não é amigável em direção de pequenos pedidos](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx), que explica por que o algoritmo do Nagle mal interage com os pedidos de tabela e fila e mostra como desabilitá-lo no seu cliente aplicação.  

### <a name="schema"></a>Esquema
Como representar e consultar os seus dados é o único fator maior que afeta o desempenho do serviço tabela. Embora cada aplicativo é diferente, esta secção descreve algumas práticas comprovadas gerais que se relacionam com:  

* Design da tabela
* Consultas eficientes
* Atualizações de dados eficiente  

#### <a name="subheading27"></a>Tabelas e partições
Tabelas são divididas em partições. Todas as entidades armazenadas numa partição compartilha a mesma chave de partição e tem uma chave de linha exclusivo para o identificar dentro dessa partição. Partições fornecem benefícios, mas também introduzem limites de escalabilidade.  

* Benefícios: Pode atualizar entidades na mesma partição numa única, batch transação atômica que contenha até 100 operações de armazenamento separada (limite de tamanho total de 4 MB). Supondo que o mesmo número de entidades a serem obtidas, também pode consultar dados dentro de uma única partição mais eficiente do que os dados que abrangem várias partições (embora continue a ler para obter recomendações sobre como consultar dados da tabela).
* Limite de escalabilidade: Acesso a entidades armazenados numa única partição não pode ser com balanceamento de carga porque as partições suportam transações de batches atômica. Por esse motivo, o destino de escalabilidade de uma partição de tabela individual é inferior para o serviço de tabela como um todo.  

Devido a essas características de tabelas e partições, deve adotar os seguintes princípios de design:  

* Dados que a aplicação cliente frequentemente atualizada ou consultados na mesma unidade lógica de trabalho devem estar localizados na mesma partição.  Isto pode ser devido à sua aplicação está a agregar gravações ou que pretende tirar partido das operações em lote atômica.  Além disso, dados numa única partição podem ser mais eficiente consultados numa única consulta que os dados entre partições.
* Consulta na mesma unidade lógica de trabalho (única consulta ou atualização do batch) ou de dados que a aplicação de cliente não inserção/atualização deve estar localizada em partições separadas.  Uma observação importante é que não há nenhum limite para o número de chaves de partição numa única tabela, de modo a ter milhões de chaves de partição não é um problema e não irá afetar o desempenho.  Por exemplo, se seu aplicativo for um site popular com o início de sessão do utilizador, com o ID de utilizador como a chave de partição pode ser uma boa opção.  

#### <a name="hot-partitions"></a>Partições muito ativas
Uma partição de acesso frequente é aquele que está a receber uma percentagem desproporcional de tráfego para uma conta e não pode ser com balanceamento de carga porque se trata de uma única partição.  Em geral, partições muito ativas são criadas de duas formas:  

##### <a name="subheading28"></a>Só de acréscimo e anteceder padrões únicas
O padrão "Só de acréscimo" é um em que todos (ou quase todos os) o tráfego para um determinado PK aumentam e diminui, de acordo com a hora atual.  Um exemplo é se o seu aplicativo usado a data atual como uma chave de partição para dados de registo.  Isso resulta em todas as inserções daqui para a última partição na tabela e o sistema não é possível carregar saldo porque todas as gravações vão para o end da sua tabela.  Se o volume de tráfego para essa partição exceder o destino de escalabilidade de nível de partição, em seguida, irá resultar na limitação.  É melhor para se certificar de que o tráfego é enviado para várias partições, para ativar o balanceamento de carga os pedidos em sua tabela.  

##### <a name="subheading29"></a>Dados de tráfego elevado
Se o esquema de partição resulta numa única partição que tenha apenas os dados que são utilizados muito mais do que outras partições, poderá também ver limitação como que se aproxima de partição de destino de escalabilidade para uma única partição.  É melhor para se certificar de que o esquema de partição resulta em nenhum única partição atingir as metas de escalabilidade.  

#### <a name="querying"></a>A consultar
Esta secção descreve as práticas comprovadas para consultar o serviço de tabela.  

##### <a name="subheading30"></a>Âmbito de consulta
Existem várias formas para especificar o intervalo de entidades para consulta.  Segue-se uma discussão sobre as utilizações de cada.  

Em geral, evite análises (maiores do que uma única entidade de consultas), mas se tiver de analisar, tente organizar os dados para que os dados que necessita sem verificação ou retornando quantidades significativas de entidades que não precisa de obter suas verificações.  

###### <a name="point-queries"></a>Ponto de consultas
Uma consulta de ponto obtém exatamente uma entidade. Ele faz isso ao especificar a chave de partição e a chave de linha da entidade para recuperar. Estas consultas são eficientes e deve usá-las sempre que possível.  

###### <a name="partition-queries"></a>Consultas de partição
Uma consulta de partição é uma consulta que obtém um conjunto de dados que partilham uma chave de partição comuns. Normalmente, a consulta Especifica um intervalo de valores de chave de linha ou um intervalo de valores para algumas propriedades de entidade, além de uma chave de partição. Estes são menos eficientes do que as consultas de ponto e devem ser utilizadas com moderação.  

###### <a name="table-queries"></a>Consultas de tabela
Uma consulta de tabela é uma consulta que obtém um conjunto de entidades que não partilha uma chave de partição comuns. Estas consultas não são eficientes e deve evitar se possível.  

##### <a name="subheading31"></a>Densidade de consulta
Outro fator importante na eficiência de consulta é o número de entidades retornadas em comparação com o número de entidades analisados para encontrar o conjunto retornado. Se seu aplicativo executa uma consulta de tabela com um filtro para um valor de propriedade que apenas 1% das partilhas de dados, a consulta irá analisar 100 entidades para cada uma entidade retorna. Os destinos de escalabilidade de tabela discutidos anteriormente se relacionam para o número de entidades, analisados e não o número de entidades retornadas: uma densidade de consulta baixa facilmente pode fazer com que o serviço de tabela para limitar a sua aplicação uma vez que tiver de analisar muitas entidades para obter a entidade que procura.  Consulte a secção abaixo sobre [desnormalização](#subheading34) para obter mais informações sobre como evitar isso.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitar a quantidade de dados devolvidos
###### <a name="subheading32"></a>Filtragem
Sempre que sabe que uma consulta retornará as entidades que não é necessário no aplicativo cliente, considere a utilização de um filtro para reduzir o tamanho do conjunto retornado. Embora as entidades não retornadas para o cliente ainda contam para os limites de escalabilidade, irá melhorar o desempenho da aplicação devido ao tamanho da carga de rede reduzida e a redução do número de entidades que a aplicação de cliente têm de processar.  Consulte acima observação sobre [densidade de consulta](#subheading31), no entanto, os destinos de escalabilidade relacionam ao número de entidades analisados, para que uma consulta que filtra as muitas entidades ainda pode resultar na limitação, mesmo que algumas entidades são devolvidas.  

###### <a name="subheading33"></a>Projeção
Se a aplicação cliente precisa apenas um conjunto limitado de propriedades das entidades na sua tabela, pode utilizar a projeção para limitar o tamanho do conjunto de dados retornado. Tal como acontece com filtragem, isto ajuda a reduzir a carga de rede e de processamento de cliente.  

##### <a name="subheading34"></a>Desnormalização
Ao contrário de trabalhar com bancos de dados relacionais, as práticas comprovadas para consultar com eficiência os dados de tabelas levam a desnormalização de seus dados. Ou seja, duplicar os mesmos dados em várias entidades (um para cada chave que pode utilizar para localizar os dados) para minimizar o número de entidades que uma consulta tiver de analisar para encontrar os dados do cliente tem de, em vez de precisar de analisar um grande número de entidades para encontrar os dados de seu aplicativo unto precisa.  Por exemplo, num site de comércio eletrônico, talvez queira localizar um pedido tanto pelo ID de cliente (dê-me pedidos este cliente) e pela data (dê-me encomendas numa data).  No armazenamento de tabelas, é melhor armazenar a entidade (ou uma referência a ele) duas vezes – uma vez com o nome da tabela, PK e var para facilitar a localizar pelo cliente ID, uma vez para facilitar a considerar na data.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete
Esta secção descreve as práticas comprovadas para modificar entidades armazenadas no serviço tabela.  

##### <a name="subheading35"></a>Criação de batches
Transações de batches são conhecidas como transações de grupo de entidade (ETG) no armazenamento do Azure; todas as operações dentro de um ETG tem de ser numa única partição numa única tabela. Sempre que possível, utilize ETGs para realizar inserções, atualizações e exclusões em lotes. Isso reduz o número de ida e volta da sua aplicação de cliente para o servidor, reduz o número de transações cobráveis (um ETG equivale a uma única transação para fins de faturação e pode conter até 100 operações de armazenamento) e permite atualizações atômicas (tudo operações do êxito ou efetuar a ativação dentro de um ETG). Ambientes com altas latências, tais como dispositivos móveis beneficiarão muito ETGs a utilizar.  

##### <a name="subheading36"></a>Upsert
Tabela de utilização **Upsert** operações sempre que possível. Existem dois tipos de **Upsert**, que podem ser mais eficiente do que um tradicional **inserir** e **atualização** operações:  

* **InsertOrMerge**: Utilize esta opção quando deseja carregar um subconjunto de propriedades da entidade, mas não tem a certeza se a entidade já existe. Se a entidade existe, essa chamada atualiza as propriedades incluídas no **Upsert** operação e deixa de todas as propriedades existentes como estão, se a entidade não existir, ele insere a nova entidade. Isso é semelhante a usar a projeção numa consulta, só precisa de carregar as propriedades que estão mudando.
* **InsertOrReplace**: Utilize esta opção quando pretender carregar uma totalmente nova entidade, mas não tiver a certeza se já existe. Só deve utilizar isso, quando sabe que a entidade recém-carregada está totalmente correta porque ele substitui completamente a entidade antiga. Por exemplo, que pretende atualizar a entidade que armazena a localização atual de um usuário, independentemente de estar ou não a aplicação anteriormente armazenou os dados de localização para o usuário; a nova entidade de localização está concluída, e não tem quaisquer informações de qualquer entidade anterior.

##### <a name="subheading37"></a>Armazenamento de série de dados numa única entidade
Às vezes, um aplicativo armazena uma série de dados que com freqüência precisar de obter ao mesmo tempo: por exemplo, uma aplicação pode controlar a utilização da CPU ao longo do tempo para desenhar um gráfico sem interrupção dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada entidade que representa uma hora específica e armazenar a utilização da CPU durante essa hora. Para representar estes dados, a aplicação tem de obter as entidades que contém os dados a partir das 24 horas mais recentes.  

Em alternativa, o seu aplicativo pode armazenar a utilização da CPU para cada hora, como uma propriedade separada de uma entidade única: para atualizar a cada hora, seu aplicativo pode usar uma única **InsertOrMerge Upsert** chamada para atualizar o valor para o mais recente hora. Para representar os dados, o aplicativo só precisa obter uma única entidade em vez de 24, tornando-se para uma consulta eficiente (veja acima a discussão sobre [âmbito de consulta](#subheading30)).

##### <a name="subheading38"></a>Armazenar dados estruturados em blobs
Dados estruturados, às vezes, parece que ela deve ir em tabelas, mas intervalos de entidades sempre são obtidos em conjunto e pode ser inserido batch.  Um bom exemplo disso é um ficheiro de registo.  Neste caso, pode vários minutos de registos do batch, inseri-las e, em seguida, sempre estará recuperando vários minutos de registos ao mesmo tempo também.  Neste caso, para desempenho, é melhor utilizar blobs, em vez de tabelas, uma vez que pode reduzir significativamente o número de objetos gravados/retornados, bem como normalmente o número de pedidos que necessitam de feitas.  

## <a name="queues"></a>Filas
Para além de práticas comprovadas para [todos os serviços](#allservices) descrito anteriormente, as seguintes práticas comprovadas aplicam-se ao serviço fila.  

### <a name="subheading39"></a>Limites de escalabilidade
Uma fila única pode processar aproximadamente 2000 mensagens (1 KB cada) por segundo (cada contagem AddMessage, GetMessage e DeleteMessage como uma mensagem aqui). Se isto não for suficiente para a sua aplicação, deve utilizar várias filas e distribuir as mensagens entre eles.  

Ver destinos de escalabilidade atuais em [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle desativado
Consulte a seção sobre configuração de tabela que discute o algoritmo de Nagle — o algoritmo de Nagle é geralmente útil para o desempenho de pedidos de fila, e deve desabilitá-la.  

### <a name="subheading41"></a>Tamanho da mensagem
Fila de desempenho e escalabilidade diminui à medida que aumenta de tamanho de mensagem. Deve colocar apenas as informações que precisa do recetor numa mensagem.  

### <a name="subheading42"></a>Obtenção de batch
Pode recuperar até 32 mensagens da fila numa única operação. Isso pode reduzir o número de idas e voltas da aplicação cliente, que é especialmente útil para ambientes, como dispositivos móveis, com latência elevada.  

### <a name="subheading43"></a>Intervalo de consulta de fila
A maioria dos aplicativos consultar mensagens de uma fila, que pode ser uma das origens de maior de transações para essa aplicação. Selecione o seu intervalo de consulta com sabedoria: consulta com demasiada frequência pode fazer com que seu aplicativo de abordar os destinos de escalabilidade para a fila. No entanto, em 200 000 transações de US $0,01 (no momento da escrita), um único processador, uma vez a cada segundo num mês iria custar menos de 15 centavos, então, custos de consulta não é, normalmente, um fator que afeta a sua escolha de intervalo de consulta.  

Para informações de custo atualizado, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Pode usar **UpdateMessage** para aumentar o tempo limite de invisibilidade ou para atualizar informações de estado de uma mensagem. Embora isso seja eficiente, lembre-se de que cada **UpdateMessage** operação conta para o destino de escalabilidade. No entanto, isso pode ser uma abordagem muito mais eficiente do que um fluxo de trabalho que passa um trabalho de uma fila para o próximo, como cada passo da tarefa é concluído. Utilizar o **UpdateMessage** operação permite que seu aplicativo guardar o estado da tarefa para a mensagem e, em seguida, continuar a trabalhar, em vez de voltar colocação em fila a mensagem para a próxima etapa do trabalho sempre que uma etapa de conclusão.  

Para obter mais informações, consulte o artigo [como: Alterar os conteúdos de uma mensagem em fila](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Arquitetura de aplicativos
Deve usar filas para tornar a sua arquitetura de aplicativos escalonáveis. A seguir listam-se algumas formas de usar filas para tornar a sua aplicação mais dimensionável:  

* Pode utilizar filas para criar listas de pendências do trabalho para processamento e suavizar as cargas de trabalho em seu aplicativo. Por exemplo, poderia colocar em fila pedidos de utilizadores para realizar o trabalho com utilização intensiva de processador, como o redimensionamento de imagens carregadas.
* Pode utilizar filas para separar partes de seu aplicativo para que pode ajustá-las independentemente. Por exemplo, um front-end da web pode colocar os resultados de pesquisa de usuários numa fila para posterior análise e armazenamento. Pode adicionar mais instâncias de função de trabalho para processar os dados de fila, conforme necessário.  

## <a name="conclusion"></a>Conclusão
Este artigo abordou a algumas das mais comuns e práticas comprovadas para otimizar o desempenho ao utilizar o armazenamento do Azure. Aconselhamos cada programador de aplicações a avaliar as respetivas aplicações com base nas práticas acima apresentadas e a considerar adotar as recomendações dadas no sentido de obter um melhor desempenho para as aplicações que utilizem o Armazenamento do Azure.

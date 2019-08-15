---
title: Lista de verificação de escalabilidade e desempenho do armazenamento do Azure | Microsoft Docs
description: Uma lista de verificação de práticas comprovadas para uso com o armazenamento do Azure no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 48a5484e2b2b663d0046fc628c02e656c5bd7a25
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985156"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure

Desde o lançamento dos serviços de Armazenamento do Microsoft Azure, a Microsoft desenvolveu uma série de práticas comprovadas para usar esses serviços de maneira eficaz, e este artigo serve para consolidar o mais importante deles em uma lista de estilos de listas de verificação. A intenção deste artigo é ajudar os desenvolvedores de aplicativos a verificar se estão usando práticas comprovadas com o armazenamento do Azure e para ajudá-los a identificar outras práticas comprovadas que eles devem considerar adotar. Este artigo não tenta abordar todas as possíveis otimizações de desempenho e escalabilidade — ele exclui os que são pequenos em seu impacto ou não é amplamente aplicável. Na medida em que o comportamento do aplicativo pode ser previsto durante o design, é útil manter isso em mente no início para evitar designs que serão executados em problemas de desempenho.  

Todos os desenvolvedores de aplicativos que usam o armazenamento do Azure devem levar algum tempo para ler este artigo e verificar se o aplicativo segue cada uma das práticas comprovadas listadas abaixo.  

## <a name="checklist"></a>Lista de verificação

Este artigo organiza as práticas comprovadas nos grupos a seguir. Práticas comprovadas aplicáveis a:  

* Todos os serviços de armazenamento do Azure (BLOBs, tabelas, filas e arquivos)
* Blobs
* Tabelas
* Filas  

| Concluído | Área | Category | Pergunta |
| --- | --- | --- | --- |
| &nbsp; | Todos os Serviços |Metas de escalabilidade |[Seu aplicativo foi projetado para evitar a abordagem das metas de escalabilidade?](#subheading1) |
| &nbsp; | Todos os Serviços |Metas de escalabilidade |[Sua Convenção de nomenclatura é projetada para permitir melhor balanceamento de carga?](#subheading47) |
| &nbsp; | Todos os Serviços |Redes |[Os dispositivos do lado do cliente têm uma largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#subheading2) |
| &nbsp; | Todos os Serviços |Redes |[Os dispositivos do lado do cliente têm um link de alta qualidade suficiente?](#subheading3) |
| &nbsp; | Todos os Serviços |Redes |[O aplicativo cliente está localizado "perto" da conta de armazenamento?](#subheading4) |
| &nbsp; | Todos os Serviços |Distribuição de Conteúdos |[Você está usando uma CDN para distribuição de conteúdo?](#subheading5) |
| &nbsp; | Todos os Serviços |Acesso direto do cliente |[Você usa SAS e CORS para permitir o acesso direto ao armazenamento em vez do proxy?](#subheading6) |
| &nbsp; | Todos os Serviços |Colocação em Cache |[O aplicativo armazena em cache os dados que são usados repetidamente e as alterações raramente?](#subheading7) |
| &nbsp; | Todos os Serviços |Colocação em Cache |[O aplicativo faz atualizações em lote (armazenando em cache o lado do cliente e, em seguida, carregando em conjuntos maiores)?](#subheading8) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Você configurou seu cliente para usar um número suficiente de conexões simultâneas?](#subheading9) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Você configurou o .NET para usar um número suficiente de threads?](#subheading10) |
| &nbsp; | Todos os Serviços |Configuração do .NET |[Você está usando o .NET 4,5 ou posterior, que melhorou a coleta de lixo?](#subheading11) |
| &nbsp; | Todos os Serviços |Paralelismo |[Você garantiu que o paralelismo está limitado adequadamente para que você não sobrecarregar os recursos do cliente ou as metas de escalabilidade?](#subheading12) |
| &nbsp; | Todos os Serviços |Ferramentas |[Você está usando a versão mais recente das bibliotecas e ferramentas de cliente fornecidas pela Microsoft?](#subheading13) |
| &nbsp; | Todos os Serviços |Tentativas |[Você está usando uma política de repetição de retirada exponencial para limitação de erros e tempos limite?](#subheading14) |
| &nbsp; | Todos os Serviços |Tentativas |[Seu aplicativo está evitando repetições para erros sem nova tentativa?](#subheading15) |
| &nbsp; | Blobs |Metas de escalabilidade |[Você tem um grande número de clientes acessando um único objeto simultaneamente?](#subheading46) |
| &nbsp; | Blobs |Metas de escalabilidade |[Seu aplicativo permanece dentro da largura de banda ou destino de escalabilidade de operações para um único blob?](#subheading16) |
| &nbsp; | Blobs |Copiando BLOBs |[Você está copiando blobs de maneira eficiente?](#subheading17) |
| &nbsp; | Blobs |Copiando BLOBs |[Você está usando AzCopy para cópias em massa de BLOBs?](#subheading18) |
| &nbsp; | Blobs |Copiando BLOBs |[Você está usando a importação/exportação do Azure para transferir grandes volumes de dados?](#subheading19) |
| &nbsp; | Blobs |Usar metadados |[Você está armazenando metadados usados com frequência sobre BLOBs em seus metadados?](#subheading20) |
| &nbsp; | Blobs |Carregando rapidamente |[Ao tentar carregar um blob rapidamente, você está carregando blocos em paralelo?](#subheading21) |
| &nbsp; | Blobs |Carregando rapidamente |[Ao tentar carregar vários BLOBs rapidamente, você está carregando BLOBs em paralelo?](#subheading22) |
| &nbsp; | Blobs |Tipo de blob correto |[Você está usando blobs de página ou BLOBs de blocos quando apropriado?](#subheading23) |
| &nbsp; | Tabelas |Metas de escalabilidade |[Você está se aproximando das metas de escalabilidade para entidades por segundo?](#subheading24) |
| &nbsp; | Tabelas |Configuração |[Você está usando JSON para suas solicitações de tabela?](#subheading25) |
| &nbsp; | Tabelas |Configuração |[Você desligou o Nagle para melhorar o desempenho de pequenas solicitações?](#subheading26) |
| &nbsp; | Tabelas |Tabelas e partições |[Você particionou corretamente os dados?](#subheading27) |
| &nbsp; | Tabelas |Partições ativas |[Você está evitando padrões somente de acréscimo e somente para preceder?](#subheading28) |
| &nbsp; | Tabelas |Partições ativas |[Suas inserções/atualizações se espalham por várias partições?](#subheading29) |
| &nbsp; | Tabelas |Escopo da consulta |[Você criou seu esquema para permitir que as consultas de ponto sejam usadas na maioria dos casos e as consultas de tabela sejam usadas com moderação?](#subheading30) |
| &nbsp; | Tabelas |Densidade da consulta |[Suas consultas normalmente só verificam e retornam linhas que seu aplicativo usará?](#subheading31) |
| &nbsp; | Tabelas |Limitando dados retornados |[Você está usando a filtragem para evitar o retorno de entidades que não são necessárias?](#subheading32) |
| &nbsp; | Tabelas |Limitando dados retornados |[Você está usando a projeção para evitar o retorno de propriedades que não são necessárias?](#subheading33) |
| &nbsp; | Tabelas |Desnormalização |[Você desnormalizou seus dados de forma a evitar consultas ineficientes ou várias solicitações de leitura ao tentar obter dados?](#subheading34) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Você está em lotes de solicitações que precisam ser transacionais ou podem ser feitas ao mesmo tempo para reduzir viagens de ida e volta?](#subheading35) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Você está evitando a recuperação de uma entidade apenas para determinar se deve chamar INSERT ou Update?](#subheading36) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Você considerou o armazenamento de uma série de dados que frequentemente serão recuperados juntos em uma única entidade como propriedades em vez de várias entidades?](#subheading37) |
| &nbsp; | Tabelas |Insert/Update/Delete |[Para entidades que sempre serão recuperadas juntas e podem ser gravadas em lotes (por exemplo, dados de série temporal), você considerou o uso de BLOBs em vez de tabelas?](#subheading38) |
| &nbsp; | Filas |Metas de escalabilidade |[Você está se aproximando das metas de escalabilidade para mensagens por segundo?](#subheading39) |
| &nbsp; | Filas |Configuração |[Você desligou o Nagle para melhorar o desempenho de pequenas solicitações?](#subheading40) |
| &nbsp; | Filas |Tamanho da Mensagem |[Suas mensagens são compactadas para melhorar o desempenho da fila?](#subheading41) |
| &nbsp; | Filas |Recuperação em massa |[Você está recuperando várias mensagens em uma única operação "Get"?](#subheading42) |
| &nbsp; | Filas |Frequência de sondagem |[Você está sondando com frequência suficiente para reduzir a latência percebida do seu aplicativo?](#subheading43) |
| &nbsp; | Filas |Atualizar mensagem |[Você está usando o UpdateMessage para armazenar o progresso no processamento de mensagens, evitando ter de reprocessar a mensagem inteira se ocorrer um erro?](#subheading44) |
| &nbsp; | Filas |Arquitetura |[Você está usando filas para tornar todo o aplicativo mais escalonável, mantendo cargas de trabalho demoradas fora do caminho crítico e dimensionamento de forma independente?](#subheading45) |

## <a name="allservices"></a>Todos os serviços

Esta seção lista as práticas comprovadas que se aplicam ao uso de qualquer um dos serviços de armazenamento do Azure (BLOBs, tabelas, filas ou arquivos).  

### <a name="subheading1"></a>Metas de escalabilidade

O armazenamento do Azure em si tem um limite de 250 contas de armazenamento por região por assinatura. Se o atingir, não poderá criar mais contas de armazenamento nessa combinação subscrição/região.

Cada um dos serviços de armazenamento do Azure tem metas de escalabilidade para capacidade (GB), taxa de transações e largura de banda. Se seu aplicativo se aproximar ou exceder qualquer um dos destinos de escalabilidade, ele poderá encontrar maiores latências ou limitação de transação. Quando um serviço de armazenamento limita seu aplicativo, o serviço começa a retornar os códigos de erro "503 Server Busy" ou "500 tempo limite de operação" para algumas transações de armazenamento. Esta seção aborda a abordagem geral para lidar com metas de escalabilidade e metas de escalabilidade de largura de banda em particular. As seções posteriores que lidam com serviços de armazenamento individuais discutem metas de escalabilidade no contexto desse serviço específico:  

* [Largura de banda e solicitações de blob por segundo](#subheading16)
* [Entidades de tabela por segundo](#subheading24)
* [Mensagens de fila por segundo](#subheading39)  

#### <a name="sub1bandwidth"></a>Meta de escalabilidade da largura de banda para todos os serviços

No momento da elaboração do artigo, as metas de largura de banda nos EUA para uma conta de armazenamento com redundância geográfica (GRS) são 10 gigabits por segundo (Gbps) para entrada (dados enviados para a conta de armazenamento) e 20 Gbps para saída (dados enviados da conta de armazenamento). Para uma conta de LRS (armazenamento com redundância local), os limites são mais altos – 20 Gbps para entrada e 30 Gbps para saída.  Os limites de largura de banda internacional podem ser menores e podem ser encontrados em nossa [página de metas](https://msdn.microsoft.com/library/azure/dn249410.aspx)de escalabilidade.  Para obter mais informações sobre as opções de redundância de armazenamento, consulte os links em recursos úteis abaixo.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>O que fazer ao abordar uma meta de escalabilidade

Se você estiver se aproximando do limite de contas de armazenamento que você pode ter em uma combinação de assinatura/região específica, avalie seu aplicativo e o uso de contas de armazenamento e determine se alguma dessas condições se aplica.

* Usar contas de armazenamento como discos não gerenciados e adicionar esses discos às suas máquinas virtuais. Nesse cenário, recomendamos o uso de [discos gerenciados](../../virtual-machines/windows/managed-disks-overview.md), pois eles lidam com a escalabilidade do disco de armazenamento para você sem a necessidade de criar e gerenciar contas de armazenamento individuais.
* Usar uma conta de armazenamento por cliente, para fins de isolamento de dados. Nesse cenário, é recomendável usar contêineres de armazenamento para cada cliente em vez de uma conta de armazenamento inteira. O armazenamento do Azure agora permite que você especifique o controle de acesso baseado em função em uma [base por contêiner](storage-auth-aad-rbac-portal.md).
* Usar várias contas de armazenamento para fragmentar para maior escalabilidade de entrada/saída/IOPS/capacidade. Nesse cenário, se possível, recomendamos que você aproveite os [limites maiores](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) de contas de armazenamento padrão para reduzir o número de contas de armazenamento necessárias para sua carga de trabalho.

Se seu aplicativo estiver se aproximando das metas de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:  

* Reconsidere a carga de trabalho que faz com que seu aplicativo aborde ou exceda a meta de escalabilidade. Você pode criá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
* Se um aplicativo precisar exceder um dos destinos de escalabilidade, você deverá criar várias contas de armazenamento e particionar os dados do aplicativo entre essas várias contas de armazenamento. Se você usar esse padrão, certifique-se de projetar seu aplicativo para que você possa adicionar mais contas de armazenamento no futuro para o balanceamento de carga. No momento da gravação, cada assinatura do Azure pode ter até 250 contas de armazenamento por região (quando implantada com o modelo de Azure Resource Manager).  As contas de armazenamento também não têm nenhum custo além do uso em termos de dados armazenados, transações feitas ou dados transferidos.
* Se o seu aplicativo atingir as metas de largura de banda, considere compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados para o serviço de armazenamento.  Embora isso possa economizar largura de banda e melhorar o desempenho da rede, ele também pode ter alguns impactos negativos.  Você deve avaliar o impacto sobre o desempenho disso devido aos requisitos de processamento adicionais para compactar e descomprimir dados no cliente. Além disso, o armazenamento de dados compactados pode dificultar a solução de problemas, pois pode ser mais difícil exibir dados armazenados usando ferramentas padrão.
* Se seu aplicativo atingir as metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas ( [](#subheading14)consulte repetições).  É melhor ter certeza de que você nunca abordará as metas de escalabilidade (usando um dos métodos acima), mas isso garantirá que seu aplicativo não continue tentando rapidamente, tornando a limitação pior.  

#### <a name="useful-resources"></a>Recursos úteis

Os links a seguir fornecem detalhes adicionais sobre destinos de escalabilidade:

* Consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md) para obter informações sobre metas de escalabilidade.
* Consulte [replicação do armazenamento do Azure](storage-redundancy.md) e a postagem do blog [Opções de redundância de armazenamento do Azure e armazenamento](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) com redundância geográfica com acesso de leitura para obter informações sobre as opções de redundância de armazenamento.
* Para obter informações atuais sobre os preços dos serviços do Azure, consulte [preços do Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Convenção de nomenclatura de partição

O armazenamento do Azure usa um esquema de particionamento baseado em intervalo para dimensionar e balancear a carga do sistema. A chave de partição (conta + contêiner + BLOB) é usada para particionar dados em intervalos e esses intervalos têm balanceamento de carga no sistema. Isso significa que as convenções de nomenclatura, como a ordenaçãolexical (por exemplo, mypayroll, myperformance, MyEmployees etc.) ou o uso de carimbos de data/hora (*log20160101*, *log20160102*, *log20160102*etc.), serão em si, as partições estão potencialmente colocalizadas no mesmo servidor de partição, até que uma operação de balanceamento de carga as divida em intervalos menores. Por exemplo, todos os BLOBs dentro de um contêiner podem ser servidos por um único servidor até que a carga nesses BLOBs exija um novo balanceamento dos intervalos de partição. Da mesma forma, um grupo de contas levemente carregadas com seus nomes organizados em ordem lexical pode ser servido por um único servidor até que a carga em uma ou todas essas contas exija que eles sejam divididos em vários servidores de partições. Cada operação de balanceamento de carga pode afetar a latência de chamadas de armazenamento durante a operação. A capacidade do sistema de lidar com uma intermitência repentina de tráfego para uma partição é limitada pela escalabilidade de um único servidor de partição até que a operação de balanceamento de carga seja iniciada e rebalancee o intervalo de chaves de partição.

Você pode seguir algumas práticas recomendadas para reduzir a frequência dessas operações.  

* Se possível, use o blob Put maior ou os tamanhos de bloco put (maior que 4 MiB para contas padrão e maior que 256 KiB para contas Premium) para ativar o HTBB (High-exthroughput Block BLOB). O HTBB fornece ingestão de alto desempenho que não é afetada pela nomenclatura de partição.
* Examine com atenção a Convenção de nomenclatura usada para contas, contêineres, BLOBs, tabelas e filas. Considere a possibilidade de prefixar nomes de conta, contêiner ou BLOB com um hash de 3 dígitos usando uma função de hash que melhor atenda às suas necessidades.  
* Se você organizar seus dados usando carimbos de data/hora ou identificadores numéricos, você precisará garantir que não esteja usando padrões de tráfego somente de acréscimo (ou somente preceder). Esses padrões não são adequados para um sistema de particionamento baseado em intervalo e podem levar a todo o tráfego indo para uma única partição e limitar o sistema contra o balanceamento de carga efetivamente. Por exemplo, se você tiver operações diárias que usam um objeto de blob com um carimbo de data/hora como *aaaammdd*, todo o tráfego dessa operação diária será direcionado para um único objeto, que é servido por um único servidor de partição. Observe se os limites por blob e os limites por partição atendem às suas necessidades e considere dividir essa operação em vários BLOBs, se necessário. Da mesma forma, se você armazenar dados de série temporal em suas tabelas, todo o tráfego poderá ser direcionado para a última parte do namespace de chave. Se você precisar usar carimbos de data/hora ou IDs numéricas, Prefixe a ID com um hash de 3 dígitos ou, no caso de carimbos de data/hora, Prefixe a parte de segundos do tempo, como *ssaaaammdd*. Se as operações de listagem e consulta forem executadas rotineiramente, escolha uma função de hash que limitará o número de consultas. Em outros casos, um prefixo aleatório pode ser suficiente.  
* Para obter informações adicionais sobre o esquema de particionamento usado no armazenamento do Azure [, consulte armazenamento do Azure: Um serviço de armazenamento em nuvem altamente disponível com](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)consistência forte.

### <a name="networking"></a>Redes

Embora a API chame a questão, muitas vezes as restrições de rede física do aplicativo têm um impacto significativo no desempenho. O descrito a seguir descreve algumas das limitações que os usuários podem encontrar.  

#### <a name="client-network-capability"></a>Funcionalidade de rede do cliente

##### <a name="subheading2"></a>Taxa

Para largura de banda, o problema costuma ser os recursos do cliente. Por exemplo, embora uma única conta de armazenamento possa lidar com 10 Gbps ou mais de entrada (consulte [metas de escalabilidade da largura de banda](#sub1bandwidth)), a velocidade da rede em uma instância de função de trabalho do Azure "pequena" é capaz de aproximadamente 100 Mbps. Instâncias maiores do Azure têm NICs com maior capacidade, portanto, você deve considerar usar uma instância maior ou mais VMs se precisar de limites de rede maiores de um único computador. Se você estiver acessando um serviço de armazenamento de um aplicativo local, a mesma regra se aplicará: compreenda os recursos de rede do dispositivo cliente e a conectividade de rede com o local de armazenamento do Azure e melhore-os conforme necessário ou crie seu aplicativo para trabalhar em seus recursos.  

##### <a name="subheading3"></a>Qualidade do link

Como com qualquer uso de rede, lembre-se de que as condições de rede que resultam em erros e perda de pacotes causarão uma taxa de transferência efetiva.  Usar o WireShark ou o NetMon pode ajudar a diagnosticar esse problema.  

##### <a name="useful-resources"></a>Recursos úteis

Para obter mais informações sobre tamanhos de máquina virtual e largura de banda alocada, consulte [tamanhos de VM do Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou tamanhos de VM do [Linux](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Local

Em qualquer ambiente distribuído, colocar o cliente perto do servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com a menor latência, o melhor local para o cliente está dentro da mesma região do Azure. Por exemplo, se você tiver um site do Azure que usa o armazenamento do Azure, você deve localizá-los em uma única região (por exemplo, oeste dos EUA ou Sudeste Asiático). Isso reduz a latência e o custo — no momento da escrita, o uso da largura de banda em uma única região é gratuito.  

Se os aplicativos cliente não estiverem hospedados no Azure (como aplicativos de dispositivos móveis ou serviços corporativos locais), em seguida, colocar novamente a conta de armazenamento em uma região próxima aos dispositivos que o acessará, geralmente reduzirá a latência. Se os clientes forem amplamente distribuídos (por exemplo, alguns em América do Norte e outros na Europa), você deverá considerar o uso de várias contas de armazenamento: uma localizada em uma região da América do Norte e outra em uma região europeia. Isso ajudará a reduzir a latência para usuários em ambas as regiões. Essa abordagem é mais fácil de implementar se os dados que o aplicativo armazena são específicos para usuários individuais e não requer a replicação de dados entre contas de armazenamento.  Para uma distribuição de conteúdo ampla, uma CDN é recomendada – consulte a próxima seção para obter mais detalhes.  

### <a name="subheading5"></a>Distribuição de conteúdo

Às vezes, um aplicativo precisa fornecer o mesmo conteúdo a muitos usuários (por exemplo, um vídeo de demonstração do produto usado na home page de um site), localizado em qualquer região ou em várias regiões. Nesse cenário, você deve usar uma CDN (rede de distribuição de conteúdo), como a CDN do Azure, e a CDN usará o armazenamento do Azure como a origem dos dados. Ao contrário de uma conta de armazenamento do Azure que existe em uma única região e que não pode fornecer conteúdo com baixa latência para outras regiões, a CDN do Azure usa servidores em vários data centers em todo o mundo. Além disso, uma CDN normalmente pode dar suporte a limites de saída muito maiores do que uma única conta de armazenamento.  

Para obter mais informações sobre a CDN do Azure, consulte [CDN do Azure](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Usando SAS e CORS

Quando você precisa autorizar um código como o JavaScript no navegador da Web de um usuário ou um aplicativo de telefone celular para acessar dados no armazenamento do Azure, uma abordagem é usar um aplicativo na função Web como um proxy: o dispositivo do usuário é autenticado com a função Web que, por sua vez, autoriza o acesso aos recursos de armazenamento. Dessa forma, você pode evitar expor suas chaves de conta de armazenamento em dispositivos não seguros. No entanto, isso coloca uma sobrecarga significativa na função Web, pois todos os dados transferidos entre o dispositivo do usuário e o serviço de armazenamento devem passar pela função Web. Você pode evitar o uso de uma função Web como um proxy para o serviço de armazenamento usando SAS (assinaturas de acesso compartilhado), às vezes em conjunto com os cabeçalhos de compartilhamento de recursos entre origens (CORS). Usando a SAS, você pode permitir que o dispositivo do usuário faça solicitações diretamente para um serviço de armazenamento por meio de um token de acesso limitado. Por exemplo, se um usuário quiser carregar uma foto em seu aplicativo, sua função Web poderá gerar e enviar para o dispositivo do usuário um token SAS que concede permissão para gravar em um BLOB ou contêiner específico pelos próximos 30 minutos (após o qual o token SAS expira).

Normalmente, um navegador não permitirá o JavaScript em uma página hospedada por um site em um domínio para executar operações específicas, como "PUT", em outro domínio. Por exemplo, se você hospedar uma função Web em "contosomarketing.cloudapp.net" e quiser usar o JavaScript do lado do cliente para carregar um blob em sua conta de armazenamento em "contosoproducts.blob.core.windows.net", a mesma política de origem dos navegadores proibirá essa operação. O CORS é um recurso de navegador que permite que o domínio de destino (nesse caso, a conta de armazenamento) se comunique com o navegador que confia nas solicitações originadas no domínio de origem (nesse caso, a função Web).  

Essas duas tecnologias podem ajudá-lo a evitar carga desnecessária (e afunilamentos) em seu aplicativo Web.  

#### <a name="useful-resources"></a>Recursos úteis

Para obter mais informações sobre SAS, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).  

Para obter mais informações sobre CORS, consulte [suporte ao compartilhamento de recursos entre origens (CORS) para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Colocação em Cache

#### <a name="subheading7"></a>Obtendo dados

Em geral, obter dados de um serviço uma vez é melhor do que obtê-lo duas vezes. Considere o exemplo de um aplicativo Web MVC em execução em uma função Web que já recuperou um blob de 50 MB do serviço de armazenamento para servir como conteúdo para um usuário. O aplicativo poderia então recuperar esse mesmo blob sempre que um usuário solicitá-lo, ou poderia armazená-lo localmente no disco e reutilizar a versão armazenada em cache para solicitações subsequentes do usuário. Além disso, sempre que um usuário solicitar os dados, o aplicativo poderá emitir GET com um cabeçalho condicional para o tempo de modificação, o que evitaria obter o blob inteiro se ele não tiver sido modificado. Você pode aplicar esse mesmo padrão para trabalhar com entidades de tabela.  

Em alguns casos, você pode decidir que seu aplicativo pode assumir que o blob permanece válido por um curto período depois de recuperá-lo e que, durante esse período, o aplicativo não precisa verificar se o blob foi modificado.

A configuração, a pesquisa e outros dados que são sempre usados pelo aplicativo são excelentes candidatos ao cache.  

Para obter mais informações sobre downloads condicionais, consulte [especificando cabeçalhos condicionais para operações de serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

#### <a name="subheading8"></a>Carregando dados em lotes

Em alguns cenários de aplicativo, você pode agregar dados localmente e, em seguida, carregá-los periodicamente em um lote em vez de carregar cada parte dos dados imediatamente. Por exemplo, um aplicativo Web pode manter um arquivo de log de atividades: o aplicativo pode carregar detalhes de cada atividade, pois ela ocorre como uma entidade de tabela (que requer muitas operações de armazenamento), ou pode salvar detalhes da atividade em um arquivo de log local e, em seguida, Carregue periodicamente todos os detalhes da atividade como um arquivo delimitado em um blob. Se cada entrada de log tiver 1 KB de tamanho, você poderá carregar milhares em uma única transação "Put Blob" (você pode carregar um blob de até 64 MB de tamanho em uma única transação). Se o computador local falhar antes do carregamento, você poderá perder alguns dados de log: o desenvolvedor do aplicativo deve criar a possibilidade de falhas no dispositivo cliente ou no carregamento.  Se os dados da atividade precisarem ser baixados para TimeSpans (não apenas uma atividade), os BLOBs serão recomendados em relação a tabelas.

### <a name="net-configuration"></a>Configuração do .NET

Se estiver usando o .NET Framework, esta seção listará várias definições de configuração rápida que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos semelhantes se aplicam à linguagem escolhida.  

#### <a name="subheading9"></a>Aumentar limite de conexão padrão

No .NET, o código a seguir aumenta o limite de conexão padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) para 100. Normalmente, você deve definir o valor para aproximadamente o número de threads usados pelo seu aplicativo.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Você deve definir o limite de conexão antes de abrir qualquer conexão.  

Para outras linguagens de programação, consulte a documentação dessa linguagem para determinar como definir o limite de conexão.  

Para obter mais informações, consulte a postagem [no blog serviços Web: Conexões](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx)simultâneas.  

#### <a name="subheading10"></a>Aumentar o número mínimo de threads se estiver usando código síncrono com tarefas assíncronas

Esse código aumentará o número mínimo de threads no pool de threads:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, consulte o [método ThreadPool. SetMinThreads](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Aproveite o .NET 4,5 e a coleta de lixo superior

Use o .NET 4,5 ou posterior para que o aplicativo cliente Aproveite as melhorias de desempenho na coleta de lixo do servidor.

Para obter mais informações, consulte o artigo [uma visão geral das melhorias de desempenho no .net 4,5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Paralelismo não associado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar o paralelismo não associado (sem limite no número de threads e/ou solicitações paralelas) para carregar ou baixar dados, usando vários trabalhos para acessar várias partições (contêineres, filas ou partições de tabela) na mesma conta de armazenamento ou para acessar vários itens na mesma partição. Se o paralelismo não estiver associado, seu aplicativo poderá exceder os recursos do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, resultando em latências mais longas e limitação.  

### <a name="subheading13"></a>Bibliotecas e ferramentas de cliente de armazenamento

Sempre use as bibliotecas e ferramentas de cliente mais recentes fornecidas pela Microsoft. No momento da gravação, há bibliotecas de cliente disponíveis para .NET, Windows Phone, Windows Runtime, Java e C++, além de bibliotecas de visualização para outros idiomas. Além disso, a Microsoft lançou os cmdlets do PowerShell e os comandos CLI do Azure para trabalhar com o armazenamento do Azure. A Microsoft desenvolve ativamente essas ferramentas com o desempenho em mente, mantém-as atualizadas com as versões de serviço mais recentes e garante que elas lidem com muitas das práticas de desempenho comprovadas internamente.  

### <a name="retries"></a>Tentativas

#### <a name="subheading14"></a>Limitação e erros de servidor ocupado

Em alguns casos, o serviço de armazenamento pode limitar seu aplicativo ou simplesmente não pode servir a solicitação devido a alguma condição transitória e retornar uma mensagem "503 Server Busy" ou "500 timeout".  Isso pode acontecer se seu aplicativo estiver se aproximando de qualquer um dos destinos de escalabilidade ou se o sistema estiver reequilibrando os dados particionados para permitir uma maior taxa de transferência.  O aplicativo cliente normalmente deve repetir a operação que causa esse erro: tentar a mesma solicitação mais tarde pode ter sucesso. No entanto, se o serviço de armazenamento estiver limitando seu aplicativo porque ele está excedendo destinos de escalabilidade, ou mesmo que o serviço não tenha sido capaz de atender à solicitação por algum outro motivo, as repetições agressivas geralmente dificultam o problema. Por esse motivo, você deve usar uma retirada exponencial (as bibliotecas de cliente assumem como padrão esse comportamento). Por exemplo, seu aplicativo pode tentar novamente após 2 segundos, depois de 4 segundos, 10 segundos, 30 segundos e, em seguida, desistir completamente. Esse comportamento resulta em seu aplicativo, reduzindo significativamente sua carga no serviço, em vez de exacerbar quaisquer problemas.  

Os erros de conectividade podem ser repetidos imediatamente, porque eles não são o resultado da limitação e devem ser transitórios.  

#### <a name="subheading15"></a>Erros sem nova tentativa

As bibliotecas de cliente estão cientes de quais erros são capazes de tentar novamente e quais não são. No entanto, se você estiver escrevendo seu próprio código na API REST de armazenamento, lembre-se de que há alguns erros que você não deve tentar novamente: por exemplo, uma resposta 400 (solicitação inválida) indica que o aplicativo cliente enviou uma solicitação que não pôde ser processada porque não foi em um formato esperado. Reenviar essa solicitação resultará na mesma resposta a cada vez, portanto, não há nenhum ponto em tentar novamente. Se você estiver escrevendo seu próprio código na API REST de armazenamento, lembre-se do que os códigos de erro significam e a maneira apropriada de tentar novamente (ou não) para cada um deles.  

#### <a name="useful-resources"></a>Recursos úteis

Para obter mais informações sobre códigos de erro de armazenamento, consulte [status e códigos de erro](https://msdn.microsoft.com/library/azure/dd179382.aspx) no site da Microsoft Azure.  

## <a name="blobs"></a>Blobs

Além das práticas comprovadas para [todos os serviços](#allservices) descritos anteriormente, as práticas comprovadas a seguir se aplicam especificamente ao serviço BLOB.  

### <a name="blob-specific-scalability-targets"></a>Metas de escalabilidade específicas do blob

#### <a name="subheading46"></a>Vários clientes acessando um único objeto simultaneamente

Se você tiver um grande número de clientes acessando um único objeto simultaneamente, será necessário considerar os destinos de escalabilidade de conta de armazenamento e de objeto. O número exato de clientes que podem acessar um único objeto variará dependendo de fatores como o número de clientes que solicitam o objeto simultaneamente, o tamanho do objeto, as condições de rede etc.

Se o objeto puder ser distribuído por meio de uma CDN, como imagens ou vídeos servidos de um site, você deverá usar uma CDN. Consulte [aqui](#subheading5).

Em outros cenários, como simulações científicas em que os dados são confidenciais, você tem duas opções. A primeira é escalonar o acesso de sua carga de trabalho, de modo que o objeto seja acessado por um período de tempo vs sendo acessado simultaneamente. Como alternativa, você pode copiar temporariamente o objeto para várias contas de armazenamento, aumentando assim o total de IOPS por objeto e entre contas de armazenamento. Em testes limitados, descobrimos que cerca de 25 VMs podiam baixar simultaneamente um blob de 100 GB em paralelo (cada VM estava paralelizando o download usando threads 32). Se você tivesse 100 clientes que precisam acessar o objeto, primeiro copie-o para uma segunda conta de armazenamento e, em seguida, faça com que as primeiras 50 VMs acessem o primeiro BLOB e o segundo 50 VMs acessam o segundo BLOB. Os resultados variam dependendo do comportamento de seus aplicativos, portanto, você deve testá-lo durante o design. 

#### <a name="subheading16"></a>Largura de banda e operações por blob

Você pode ler ou gravar em um único blob de até um máximo de 60 MB/segundo (isso é aproximadamente 480 Mbps, o que excede os recursos de muitas redes do lado do cliente (incluindo a NIC física no dispositivo cliente). Além disso, um único blob dá suporte a até 500 solicitações por segundo. Se você tiver vários clientes que precisam ler o mesmo BLOB e você pode exceder esses limites, considere usar uma CDN para distribuir o blob.  

Para obter mais informações sobre a taxa de transferência de destino para BLOBs, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Copiando e movendo BLOBs

#### <a name="subheading17"></a>Copiar blob

A API REST de armazenamento versão 2012-02-12 introduziu a capacidade útil de copiar BLOBs entre contas: um aplicativo cliente pode instruir o serviço de armazenamento a copiar um blob de outra fonte (possivelmente em uma conta de armazenamento diferente) e, em seguida, permitir que o serviço execute a cópia assincronamente. Isso pode reduzir significativamente a largura de banda necessária para o aplicativo quando você está migrando dados de outras contas de armazenamento, pois não é necessário baixar e carregar os dados.  

No entanto, uma consideração é que, ao copiar entre contas de armazenamento, não há nenhum tempo de garantia de quando a cópia será concluída. Se seu aplicativo precisar concluir uma cópia de blob rapidamente sob seu controle, talvez seja melhor copiar o blob baixando-o para uma VM e, em seguida, carregando-o para o destino.  Para uma previsibilidade completa nessa situação, verifique se a cópia é executada por uma VM em execução na mesma região do Azure ou se as condições de rede podem (e provavelmente irão) afetar o desempenho da cópia.  Além disso, você pode monitorar o progresso de uma cópia assíncrona de forma programática.  

As cópias na mesma conta de armazenamento geralmente são concluídas rapidamente.  

Para obter mais informações, consulte [copiar blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Usar AzCopy

A equipe de armazenamento do Azure lançou uma ferramenta de linha de comando "AzCopy", destinada a ajudar a transferir em massa muitos BLOBs para, de e entre as contas de armazenamento.  Essa ferramenta é otimizada para esse cenário e pode alcançar altas taxas de transferência.  Seu uso é incentivado para cenários de upload, download e cópia em massa. Para saber mais sobre ele e baixá-lo, consulte [transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Serviço de importação/exportação do Azure

Para grandes volumes de dados (mais de 1 TB), o armazenamento do Azure oferece o serviço de importação/exportação, que permite carregar e baixar do armazenamento de BLOBs enviando discos rígidos.  Você pode colocar seus dados em um disco rígido e enviá-los à Microsoft para carregar ou enviar um disco rígido em branco para a Microsoft para baixar dados.  Para obter mais informações, veja [Utilizar o Serviço de Importação/Exportação do Microsoft Azure para Transferir Dados para o Armazenamento de Blobs](../storage-import-export-service.md).  Isso pode ser muito mais eficiente do que carregar/baixar esse volume de dados pela rede.  

### <a name="subheading20"></a>Usar metadados

O serviço blob dá suporte a solicitações de cabeçalho, que podem incluir metadados sobre o blob. Por exemplo, se seu aplicativo precisasse dos dados EXIF fora de uma foto, ele poderia recuperar a foto e extraí-la. Para economizar largura de banda e melhorar o desempenho, seu aplicativo pode armazenar os dados EXIF nos metadados do blob quando o aplicativo carregou a foto: você pode recuperar os dados EXIF nos metadados usando apenas uma solicitação HEAD, economizando largura de banda significativa e o tempo de processamento necessário para extrair os dados EXIF cada vez que o blob é lido. Isso seria útil em cenários em que você só precisa dos metadados, e não do conteúdo completo de um blob.  Somente 8 KB de metadados podem ser armazenados por blob (o serviço não aceitará uma solicitação para armazenar mais do que isso). portanto, se os dados não couberem nesse tamanho, talvez você não possa usar essa abordagem.  

### <a name="rapid-uploading"></a>Carregamento rápido

Para carregar os BLOBs rapidamente, a primeira pergunta a ser respondida é: você está carregando um ou vários BLOBs?  Use as diretrizes abaixo para determinar o método correto a ser usado dependendo do seu cenário.  

#### <a name="subheading21"></a>Carregando um blob grande rapidamente

Para carregar um único blob grande rapidamente, o aplicativo cliente deve carregar seus blocos ou páginas em paralelo (sendo cuidadoso com as metas de escalabilidade para BLOBs individuais e a conta de armazenamento como um todo).  As bibliotecas oficiais de cliente de armazenamento RTM fornecidas pela Microsoft (.NET, Java) têm a capacidade de fazer isso.  Para cada uma das bibliotecas, use o objeto/Propriedade abaixo especificado para definir o nível de simultaneidade:  

* .NET: Defina ParallelOperationThreadCount em um objeto BlobRequestOptions a ser usado.
* Java/Android: Usar BlobRequestOptions. setConcurrentRequestCount ()
* Node. js: Use parallelOperationThreadCount nas opções de solicitação ou no serviço BLOB.
* C++: Use o método blob_request_options:: set_parallelism_factor.

#### <a name="subheading22"></a>Carregando muitos BLOBs rapidamente

Para carregar vários BLOBs rapidamente, carregue os BLOBs em paralelo. Isso é mais rápido do que carregar BLOBs únicos por vez com carregamentos de bloco paralelos porque ele espalha o carregamento entre várias partições do serviço de armazenamento. Um único blob dá suporte apenas a uma taxa de transferência de 60 MB/segundo (aproximadamente 480 Mbps). No momento da gravação, uma conta LRS baseada nos EUA dá suporte a até 20 Gbps de entrada, o que é muito mais do que a taxa de transferência com suporte de um blob individual.  O [AzCopy](#subheading18) executa carregamentos em paralelo por padrão e é recomendado para esse cenário.  

### <a name="subheading23"></a>Escolhendo o tipo correto de BLOB

O armazenamento do Azure dá suporte a dois tipos de blob: blobs de *páginas* e blobs de *blocos* . Para um determinado cenário de uso, sua escolha de tipo de blob afetará o desempenho e a escalabilidade de sua solução. Blobs de blocos são apropriados quando você deseja carregar grandes quantidades de dados com eficiência: por exemplo, um aplicativo cliente pode precisar carregar fotos ou vídeo no armazenamento de BLOBs. Os blobs de páginas são apropriados se o aplicativo precisar executar gravações aleatórias nos dados: por exemplo, os VHDs do Azure são armazenados como BLOBs de páginas.  

Para obter mais informações, consulte [noções básicas sobre blobs de blocos, blobs de acréscimo e blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabelas

Além das práticas comprovadas para [todos os serviços](#allservices) descritos anteriormente, as práticas comprovadas a seguir se aplicam especificamente ao serviço tabela.  

### <a name="subheading24"></a>Metas de escalabilidade específicas de tabela

Além das limitações de largura de banda de uma conta de armazenamento inteira, as tabelas têm o seguinte limite de escalabilidade específico.  O sistema balanceará a carga conforme o tráfego aumentar, mas se o tráfego tiver picos repentinos, talvez você não consiga obter esse volume de taxa de transferência imediatamente.  Se o seu padrão tiver intermitências, você deve esperar ver a limitação e/ou os tempos limite durante a intermitência, uma vez que o serviço de armazenamento automaticamente balanceia a tabela.  O aumento lento geralmente tem resultados melhores, pois dá ao tempo do sistema para balancear a carga adequadamente.  

#### <a name="entities-per-second-storage-account"></a>Entidades por segundo (conta de armazenamento)

O limite de escalabilidade para acessar tabelas é de até 20.000 entidades (1 KB cada) por segundo para uma conta.  Em geral, cada entidade que é inserida, atualizada, excluída ou verificada conta para esse destino.  Portanto, uma inserção em lote que contém 100 entidades contaria como 100 entidades.  Uma consulta que examina as entidades de 1000 e retorna 5 conta como 1000 entidades.  

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)

Em uma única partição, a meta de escalabilidade para acessar tabelas é de 2.000 entidades (1 KB cada) por segundo, usando a mesma contagem, conforme descrito na seção anterior.  

### <a name="configuration"></a>Configuração

Esta seção lista várias definições de configuração rápida que você pode usar para fazer melhorias significativas de desempenho no serviço tabela:  

#### <a name="subheading25"></a>Usar JSON

A partir do serviço de armazenamento versão 2013-08-15, o serviço tabela dá suporte ao uso de JSON em vez do formato AtomPub baseado em XML para transferir dados da tabela. Isso pode reduzir os tamanhos de carga em até 75% e pode melhorar significativamente o desempenho do seu aplicativo.

Para obter mais informações, consulte as [tabelas de Microsoft Azure de postagem: Introdução ao](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) formato JSON e [de carga para operações de serviço tabela](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Desabilitar Nagle

O algoritmo do Nagle é amplamente implementado em redes TCP/IP como um meio para melhorar o desempenho da rede. No entanto, não é ideal em todas as circunstâncias (como ambientes altamente interativos). Para o armazenamento do Azure, o algoritmo do Nagle tem um impacto negativo sobre o desempenho de solicitações para os serviços tabela e fila, e você deve desabilitá-lo, se possível.

### <a name="schema"></a>Esquema

A maneira como você representa e consulta seus dados é o maior fator único que afeta o desempenho do serviço tabela. Embora cada aplicativo seja diferente, esta seção descreve algumas práticas comprovadas gerais relacionadas a:  

* Design de tabela
* Consultas eficientes
* Atualizações de dados eficientes  

#### <a name="subheading27"></a>Tabelas e partições

As tabelas são divididas em partições. Cada entidade armazenada em uma partição compartilha a mesma chave de partição e tem uma chave de linha exclusiva para identificá-la nessa partição. As partições fornecem benefícios, mas também apresentam limites de escalabilidade.  

* Benefícios: Você pode atualizar entidades na mesma partição em uma única transação atômica, em lote que contém até 100 operações de armazenamento separadas (limite de 4 MB de tamanho total). Supondo que o mesmo número de entidades a serem recuperadas, você também pode consultar dados em uma única partição de forma mais eficiente do que os dados que abrangem partições (embora continue lendo para obter mais recomendações sobre como consultar os dados da tabela).
* Limite de escalabilidade: O acesso a entidades armazenadas em uma única partição não pode ter balanceamento de carga porque as partições dão suporte a transações em lote atômicas. Por esse motivo, a meta de escalabilidade para uma partição de tabela individual é menor do que para o serviço tabela como um todo.  

Devido a essas características de tabelas e partições, você deve adotar os seguintes princípios de design:  

* Os dados que o aplicativo cliente com frequência atualizados ou consultados na mesma unidade lógica de trabalho devem estar localizados na mesma partição.  Isso pode ocorrer porque seu aplicativo está agregando gravações ou porque você deseja aproveitar as operações de lote atômicas.  Além disso, os dados em uma única partição podem ser consultados de forma mais eficiente em uma única consulta do que os dados entre partições.
* Os dados que o aplicativo cliente não insere/atualiza ou consultam na mesma unidade lógica de trabalho (consulta única ou atualização em lote) devem estar localizados em partições separadas.  Uma observação importante é que não há nenhum limite para o número de chaves de partição em uma única tabela, portanto, ter milhões de chaves de partição não é um problema e não afetará o desempenho.  Por exemplo, se seu aplicativo for um site popular com logon de usuário, usar a ID de usuário como a chave de partição pode ser uma boa opção.  

#### <a name="hot-partitions"></a>Partições ativas

Uma partição ativa é aquela que está recebendo uma porcentagem desproporcional do tráfego para uma conta e não pode ter balanceamento de carga porque é uma única partição.  Em geral, as partições ativas são criadas de duas maneiras:  

##### <a name="subheading28"></a>Somente acrescentar e preceder padrões

O padrão "acrescentar somente" é aquele em que todos (ou quase todos) do tráfego para uma determinada CP aumentam e diminuem de acordo com a hora atual.  Um exemplo é se seu aplicativo usou a data atual como uma chave de partição para dados de log.  Isso resulta em todas as inserções indo para a última partição em sua tabela, e o sistema não pode balancear a carga porque todas as gravações vão até o final da tabela.  Se o volume de tráfego para essa partição exceder o destino de escalabilidade no nível da partição, isso resultará em limitação.  É melhor garantir que o tráfego seja enviado para várias partições, a fim de habilitar o balanceamento de carga das solicitações em sua tabela.  

##### <a name="subheading29"></a>Dados de tráfego alto

Se o esquema de particionamento resultar em uma única partição que tem apenas dados que são muito mais usados do que outras partições, você também poderá ver a limitação, pois essa partição se aproximará da meta de escalabilidade para uma única partição.  É melhor garantir que o esquema de partição não resulte em nenhuma partição única que se aproxima das metas de escalabilidade.  

#### <a name="querying"></a>A consultar

Esta seção descreve as práticas comprovadas para consultar o serviço tabela.  

##### <a name="subheading30"></a>Escopo da consulta

Há várias maneiras de especificar o intervalo de entidades a serem consultadas.  Veja a seguir uma discussão sobre os usos de cada um.  

Em geral, evite verificações (consultas maiores que uma única entidade), mas se você precisar verificar, tente organizar seus dados para que suas verificações recuperem os dados necessários sem verificar ou retornar quantidades significativas de entidades que você não precisa.  

###### <a name="point-queries"></a>Consultas de ponto

Uma consulta de ponto recupera exatamente uma entidade. Ele faz isso especificando a chave de partição e a chave de linha da entidade a ser recuperada. Essas consultas são eficientes e você deve usá-las sempre que possível.  

###### <a name="partition-queries"></a>Consultas de partição

Uma consulta de partição é uma consulta que recupera um conjunto de dados que compartilha uma chave de partição comum. Normalmente, a consulta especifica um intervalo de valores de chave de linha ou um intervalo de valores para alguma propriedade de entidade, além de uma chave de partição. Elas são menos eficientes do que as consultas de ponto e devem ser usadas com moderação.  

###### <a name="table-queries"></a>Consultas de tabela

Uma consulta de tabela é uma consulta que recupera um conjunto de entidades que não compartilha uma chave de partição comum. Essas consultas não são eficientes e você deve evitá-las, se possível.  

##### <a name="subheading31"></a>Densidade da consulta

Outro fator importante na eficiência da consulta é o número de entidades retornadas em comparação com o número de entidades verificadas para localizar o conjunto retornado. Se seu aplicativo executar uma consulta de tabela com um filtro para um valor de propriedade que apenas 1% dos compartilhamentos de dados, a consulta examinará 100 entidades para cada entidade que retornar. As metas de escalabilidade de tabela discutidas anteriormente se relacionam com o número de entidades verificadas e não o número de entidades retornadas: uma densidade de consulta baixa pode facilmente fazer com que o serviço tabela limite seu aplicativo, pois ele deve verificar tantas entidades para Recupere a entidade que você está procurando.  Consulte a seção abaixo sobre [](#subheading34) desnormalização para obter mais informações sobre como evitar isso.  

##### <a name="limiting-the-amount-of-data-returned"></a>Limitando a quantidade de dados retornados

###### <a name="subheading32"></a>Aplica

Quando você sabe que uma consulta retornará entidades que você não precisa no aplicativo cliente, considere usar um filtro para reduzir o tamanho do conjunto retornado. Embora as entidades não retornadas ao cliente ainda sejam contadas para os limites de escalabilidade, o desempenho do aplicativo será melhorado devido ao tamanho reduzido da carga de rede e ao número reduzido de entidades que seu aplicativo cliente deve processar.  Veja a observação acima sobre a [densidade da consulta](#subheading31). no entanto, as metas de escalabilidade se relacionam com o número de entidades verificadas, de modo que uma consulta que filtra muitas entidades ainda pode resultar em limitação, mesmo que poucas entidades sejam retornadas.  

###### <a name="subheading33"></a>Projeção

Se o aplicativo cliente precisar apenas de um conjunto limitado de propriedades das entidades em sua tabela, você poderá usar a projeção para limitar o tamanho do conjunto de dados retornado. Assim como na filtragem, isso ajuda a reduzir a carga de rede e o processamento do cliente.  

##### <a name="subheading34"></a>Desnormalização

Ao contrário do trabalho com bancos de dados relacionais, as práticas comprovadas para consultar com eficiência o dado da tabela levam à desnormalização dos dados. Ou seja, duplicar os mesmos dados em várias entidades (um para cada chave que você pode usar para localizar os dados) para minimizar o número de entidades que uma consulta deve verificar para localizar os dados de que o cliente precisa, em vez de ter que verificar grandes números de entidades para localizar os dados de seu aplicativo licativo necessidades.  Por exemplo, em um site de comércio eletrônico, talvez você queira encontrar um pedido tanto pela ID do cliente (fornecer pedidos deste cliente) e pela data (fornecer pedidos em uma data).  No armazenamento de tabelas, é melhor armazenar a entidade (ou uma referência a ela) duas vezes – uma vez com o nome da tabela, CP e r para facilitar a localização pela ID do cliente, uma vez para facilitar sua localização até a data.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete

Esta seção descreve as práticas comprovadas para modificar entidades armazenadas no serviço tabela.  

##### <a name="subheading35"></a>Envio em lote

As transações em lote são conhecidas como transações de grupo de entidades (ETG) no armazenamento do Azure; todas as operações em um ETG devem estar em uma única partição em uma única tabela. Sempre que possível, use ETGs para executar inserções, atualizações e exclusões em lotes. Isso reduz o número de viagens de ida e volta de seu aplicativo cliente para o servidor, reduz o número de transações faturáveis (um ETG conta como uma única transação para fins de cobrança e pode conter até 100 operações de armazenamento) e habilita atualizações atômicas (todas as as operações tiveram êxito ou todas falham em um ETG). Ambientes com latências altas como dispositivos móveis se beneficiarão muito do uso do ETGs.  

##### <a name="subheading36"></a>Upsert

Use as operações **Upsert** de tabela sempre que possível. Há dois tipos de **Upsert**, os quais podem ser mais eficientes do que as operações tradicionais de **inserção** e **atualização** :  

* **InsertOrMerge**: Use isso quando desejar carregar um subconjunto das propriedades da entidade, mas não tiver certeza se a entidade já existe. Se a entidade existir, essa chamada atualizará as propriedades incluídas na operação **Upsert** e deixará todas as propriedades existentes como estão, se a entidade não existir, ela inserirá a nova entidade. Isso é semelhante ao uso de projeção em uma consulta, pois você só precisa carregar as propriedades que estão sendo alteradas.
* **InsertOrReplace**: Use isso quando desejar carregar uma entidade totalmente nova, mas não tiver certeza se ela já existe. Você só deve usar isso quando souber que a entidade carregada recentemente está totalmente correta porque substitui completamente a antiga entidade. Por exemplo, você deseja atualizar a entidade que armazena o local atual de um usuário, independentemente de o aplicativo ter armazenado previamente os dados de localização para o usuário; a nova entidade local está concluída e você não precisa de nenhuma informação de nenhuma entidade anterior.

##### <a name="subheading37"></a>Armazenando séries de dados em uma única entidade

Às vezes, um aplicativo armazena uma série de dados que freqüentemente precisa recuperar de uma só vez: por exemplo, um aplicativo pode controlar o uso da CPU ao longo do tempo para plotar um gráfico de rolagem dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada entidade representando uma hora específica e armazenando o uso da CPU para essa hora. Para plotar esses dados, o aplicativo precisa recuperar as entidades que contêm os dados das 24 horas mais recentes.  

Como alternativa, seu aplicativo pode armazenar o uso da CPU para cada hora como uma propriedade separada de uma única entidade: para atualizar a cada hora, seu aplicativo pode usar uma única chamada **InsertOrMerge Upsert** para atualizar o valor da hora mais recente. Para plotar os dados, o aplicativo precisa apenas recuperar uma única entidade em vez de 24, fazendo uma consulta eficiente (consulte a discussão acima sobre o [escopo da consulta](#subheading30)).

##### <a name="subheading38"></a>Armazenando dados estruturados em BLOBs

Às vezes, os dados estruturados parecem estar em tabelas, mas os intervalos de entidades são sempre recuperados juntos e podem ser inseridos em lote.  Um bom exemplo disso é um arquivo de log.  Nesse caso, você pode fazer o lote de vários minutos de logs, inseri-los e, em seguida, sempre está recuperando vários minutos de logs por vez.  Nesse caso, para desempenho, é melhor usar BLOBs em vez de tabelas, pois você pode reduzir significativamente o número de objetos gravados/retornados, bem como geralmente o número de solicitações que precisam ser feitas.  

## <a name="queues"></a>Filas

Além das práticas comprovadas para [todos os serviços](#allservices) descritos anteriormente, as práticas comprovadas a seguir se aplicam especificamente ao serviço fila.  

### <a name="subheading39"></a>Limites de escalabilidade

Uma única fila pode processar aproximadamente 2.000 mensagens (1 KB cada) por segundo (cada submensagem, GetMessage e DeleteMessage contam como uma mensagem aqui). Se isso for insuficiente para seu aplicativo, você deverá usar várias filas e espalhar as mensagens entre elas.  

Exibir metas de escalabilidade atuais em [metas de desempenho e escalabilidade do armazenamento do Azure](storage-scalability-targets.md).  

### <a name="subheading40"></a>Desabilitar Nagle

Consulte a seção sobre configuração de tabela que aborda o algoritmo Nagle – o algoritmo Nagle geralmente é insatisfatório para o desempenho de solicitações de fila, e você deve desabilitá-la.  

### <a name="subheading41"></a>Tamanho da mensagem

O desempenho e a escalabilidade da fila diminuem conforme o tamanho da mensagem aumenta. Você deve posicionar apenas as informações de que o destinatário precisa em uma mensagem.  

### <a name="subheading42"></a>Recuperação em lote

Você pode recuperar até 32 mensagens de uma fila em uma única operação. Isso pode reduzir o número de viagens de ida e volta do aplicativo cliente, o que é especialmente útil para ambientes, como dispositivos móveis, com alta latência.  

### <a name="subheading43"></a>Intervalo de sondagem de fila

A maioria dos aplicativos pesquisa mensagens de uma fila, que podem ser uma das maiores fontes de transações para esse aplicativo. Selecione seu intervalo de sondagem de maneira inteligente: a sondagem com muita frequência pode fazer com que seu aplicativo aborde as metas de escalabilidade para a fila. No entanto, às 200.000 transações para $0.01 (no momento da gravação), um único processador sondando uma vez por segundo por um mês custaria menos de 15 centavos, portanto, o custo não é normalmente um fator que afeta sua escolha de intervalo de sondagem.  

Para obter informações atualizadas sobre custos, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Atualizar mensagem

Você pode usar a operação **Atualizar mensagem** para aumentar o tempo limite de invisibilidade ou para atualizar informações de estado de uma mensagem. Embora isso seja poderoso, lembre-se de que cada operação **Atualizar mensagem** conta para o destino de escalabilidade. No entanto, isso pode ser uma abordagem muito mais eficiente do que ter um fluxo de trabalho que passa uma tarefa de uma fila para a outra, uma vez que cada etapa do trabalho é concluída. O uso da operação **Atualizar mensagem** permite que seu aplicativo salve o estado do trabalho na mensagem e continue trabalhando, em vez de enfileirar novamente a mensagem para a próxima etapa do trabalho sempre que uma etapa for concluída.  

Para obter mais informações, consulte o [artigo como: Alterar o conteúdo de uma mensagem](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message)em fila.  

### <a name="subheading45"></a>Arquitetura do aplicativo

Você deve usar filas para tornar a arquitetura do aplicativo escalonável. Veja a seguir algumas maneiras de usar filas para tornar seu aplicativo mais escalonável:  

* Você pode usar as filas do para criar registros posteriores de trabalho para processamento e suavizar cargas de trabalhos em seu aplicativo. Por exemplo, você pode enfileirar solicitações de usuários para executar o trabalho intensivo do processador, como redimensionar imagens carregadas.
* Você pode usar filas para desacoplar partes do seu aplicativo para que possa dimensioná-las de forma independente. Por exemplo, um front-end da Web poderia colocar os resultados da pesquisa de usuários em uma fila para análise e armazenamento posteriores. Você pode adicionar mais instâncias de função de trabalho para processar os dados da fila, conforme necessário.  

## <a name="conclusion"></a>Conclusão

Este artigo abordou algumas das práticas mais comuns comprovadas para otimizar o desempenho ao usar o armazenamento do Azure. Aconselhamos cada programador de aplicações a avaliar as respetivas aplicações com base nas práticas acima apresentadas e a considerar adotar as recomendações dadas no sentido de obter um melhor desempenho para as aplicações que utilizem o Armazenamento do Azure.

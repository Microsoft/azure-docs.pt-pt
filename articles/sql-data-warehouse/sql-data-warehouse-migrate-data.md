---
title: Migre os seus dados para o SQL Data Warehouse | Documentos da Microsoft
description: Dicas para migrar seus dados para o Azure SQL Data Warehouse para o desenvolvimento de soluções.
services: sql-data-warehouse
author: jrowlandjones
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: jrj
ms.reviewer: igorstan
ms.openlocfilehash: 6a2acf602252ee4319f9a5eccef53a25d8e2dd7f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014259"
---
# <a name="migrate-your-data"></a>Migre os seus dados
Dados podem ser movidos de origens diferentes para o armazém de dados SQL com ferramentas de várias.  Cópia do ADF, o SSIS e o bcp podem todas ser utilizadas para atingir esse objetivo. No entanto, como a quantidade de dados aumenta deve pensar em dividir o processo de migração de dados em etapas. Isso lhe dá a oportunidade de otimizar a cada etapa para desempenho e resiliência garantir uma migração de dados uniforme.

Este artigo aborda, em primeiro lugar, os cenários de migração simples de cópia do ADF, o SSIS e o bcp. Ele, em seguida, ver um pouco mais aprofundadamente como a migração pode ser otimizada.

## <a name="azure-data-factory-adf-copy"></a>Cópia do Data Factory (ADF) do Azure
[Cópia do ADF] [ ADF Copy] faz parte do [do Azure Data Factory][Azure Data Factory]. Pode utilizar a cópia do ADF para exportar os dados para ficheiros simples que residem no armazenamento local, para ficheiros simples remotos mantidos no armazenamento de Blobs do Azure ou diretamente para o SQL Data Warehouse.

Se seus dados é iniciado em arquivos simples, então, primeiro terá de transferir para o blob de armazenamento do Azure antes de iniciar uma carga-lo para o SQL Data Warehouse. Depois dos dados são transferidos para o armazenamento de Blobs do Azure pode optar por utilizar [cópia do ADF] [ ADF Copy] novamente para enviar os dados para o SQL Data Warehouse.

O PolyBase oferece também uma opção de alto desempenho para carregar os dados. No entanto, que significa utilizar duas ferramentas em vez de um. Se precisar do melhor desempenho, utilize o PolyBase. Se desejar uma experiência única ferramenta (e os dados não são grandes), em seguida, o ADF é sua resposta.

Siga [deste tutorial](../data-factory/load-azure-sql-data-warehouse.md) para saber como utilizar o ADF para carregar dados para o armazém de dados.

## <a name="integration-services"></a>Serviços de Integração
Integration Services (SSIS) é uma ferramenta poderosa e flexível extrair a transformação e carregamento (ETL) que suporta fluxos de trabalho complexos, de transformação de dados e de várias opções de carregamento de dados. Utilize o SSIS para simplesmente transferir dados para o Azure ou como parte de uma migração mais ampla.

> [!NOTE]
> SSIS pode exportar para UTF-8, sem a marca de ordem de byte no arquivo. Para configurar isso, primeiro tem de utilizar o componente de coluna derivada para converter os dados de caractere no fluxo de dados para utilizar a página de código UTF-8 65001. As colunas foram convertidas, escreva os dados para o adaptador de destino de ficheiro simples garantir que 65001 também foi selecionado como a página de código para o ficheiro.
> 
> 

SSIS liga-se ao SQL Data Warehouse como que iria a estabelecer ligação a uma implementação do SQL Server. No entanto, as suas ligações terá de estar a utilizar um Gestor de ligações ADO.NET. Também deve Tome cuidado para configurar a "utilização inserção em massa quando estiverem disponíveis" definição para maximizar o débito. Consulte a [adaptador de destino do ADO.NET] [ ADO.NET destination adapter] artigo para saber mais sobre esta propriedade

> [!NOTE]
> Ligar ao Azure SQL Data Warehouse utilizando OLEDB não é suportada.
> 
> 

Além disso, sempre há a possibilidade de que um pacote pode falhar devido a problemas de rede ou a limitação. Design de pacotes, de modo que podem ser retomadas no ponto de falha, sem nova execução de trabalho que foi concluída antes da falha.

Para obter mais informações, consulte a [documentação do SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
BCP é um utilitário de linha de comandos que foi concebido para a importação de dados de ficheiro simples e exportação. Alguns transformação pode ocorrer durante a exportação de dados. Para realizar transformações simples usar uma consulta para selecionar e transformar os dados. Depois de exportar, os arquivos simples podem, em seguida, ser carregados diretamente para o destino a base de dados do armazém de dados do SQL.

> [!NOTE]
> Muitas vezes, é uma boa idéia para encapsular as transformações usadas durante a exportação de dados numa vista no sistema de origem. Isto garante que a lógica é mantida e o processo é repetido.
> 
> 

Vantagens do bcp são:

* Simplicidade. comandos do BCP são simples de criar e executar
* Processo de carregamento startable novamente. Uma vez exportado a carga pode ser executado qualquer número de vezes

Limitações do bcp são:

* BCP funciona com apresentados nas tabelas em arquivos simples apenas. Não funciona com arquivos, como JSON ou xml
* Capacidades de transformação de dados estão limitadas a apenas a fase de exportação e são simples de natureza
* BCP não foi adaptado para ser robusto quando carregar os dados através da internet. Qualquer instabilidade de rede pode fazer com que um erro de carregamento.
* BCP baseia-se no esquema que estejam presentes na base de dados de destino antes da carga

Para obter mais informações, consulte [utilizar o bcp para carregar dados para o SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Otimizar a migração de dados
Um processo de migração de dados do SQL dw pode ser dividido com eficiência em três etapas separadas:

1. Exportação de dados de origem
2. Transferência de dados para o Azure
3. Carregar para a base de dados do SQL dw do destino

Cada etapa pode ser otimizada individualmente para criar um processo de migração robusto, startable novamente e resiliente que maximiza o desempenho em cada passo.

## <a name="optimizing-data-load"></a>Otimizar o carregamento de dados
Olhando para eles na ordem inversa por um momento; é a forma mais rápida para carregar dados através do PolyBase. Otimizar para um processo de carregamento PolyBase coloca pré-requisitos em passos anteriores, portanto, é melhor entender isso à cabeça. São:

1. Codificação de ficheiros de dados
2. Formato de ficheiros de dados
3. Localização dos ficheiros de dados

### <a name="encoding"></a>Codificação
O PolyBase requer ficheiros de dados a ser UTF-8 ou UTF-16FE. 



### <a name="format-of-data-files"></a>Formato de ficheiros de dados
O PolyBase estipula um terminador de linha fixo de \n ou nova linha. Os ficheiros de dados têm de cumprir esta Norma. Não existem restrições terminadores de coluna ou de cadeia de caracteres.

Terá de definir todas as colunas no ficheiro como parte da sua tabela externa em PolyBase. Certifique-se de que todas as colunas exportadas são necessárias e que os tipos de estar em conformidade com as normas necessárias.

Consulte novamente para o [migrar o seu esquema] artigo para obter detalhes sobre os tipos de dados suportados.

### <a name="location-of-data-files"></a>Localização dos ficheiros de dados
O SQL Data Warehouse utiliza o PolyBase para carregar dados do armazenamento de Blobs do Azure exclusivamente. Conseqüentemente, os dados devem primeiro transferiu para o armazenamento de Blobs.

## <a name="optimizing-data-transfer"></a>Otimização de dados de transferência
Uma das partes mais lentas de migração de dados é a transferência dos dados para o Azure. Não apenas pode ser de largura de banda de rede um problema, mas também a confiabilidade de rede pode seriamente hamper progresso. Por predefinição, migrar dados para o Azure é através da internet, de modo razoavelmente provavelmente as possibilidades de ocorrência de erros de transferência. No entanto, estes erros podem exigir dados sejam enviados novamente no todo ou em parte.

Felizmente, tem várias opções para melhorar a velocidade e a resiliência deste processo:

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Convém considerar o uso [ExpressRoute] [ ExpressRoute] para acelerar a transferência. [ExpressRoute] [ ExpressRoute] fornece com uma ligação privada estabelecida para o Azure para que a ligação não é transmitido pela internet pública. Não se trata de um passo obrigatório. No entanto, ele irá melhorar o débito quando enviar dados para o Azure, no local ou instalação de colocalização.

Os benefícios do uso [ExpressRoute] [ ExpressRoute] são:

1. Maior confiabilidade
2. Velocidade de rede mais rápida
3. Latência de rede mais baixa
4. maior segurança de rede

[ExpressRoute] [ ExpressRoute] seja benéfica para vários cenários; não apenas a migração.

Interessado? Para obter mais informações e preços, visite o [documentação do ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Importar do Azure e o serviço de exportação
O serviço de exportação e importação do Azure é um processo de transferência de dados concebido para grandes (GB + +) para grandes (TB C++) as transferências de dados para o Azure. Ele envolve a escrita de seus dados para discos e enviando-os para um centro de dados do Azure. O conteúdo do disco, em seguida, será carregado no Azure Storage Blobs em seu nome.

Uma visão geral do processo de exportação de importação é o seguinte:

1. Configurar um contentor de armazenamento de Blobs do Azure para receber os dados
2. Exportar os dados para o armazenamento local
3. Copiar os dados para 3,5 polegadas SATA II/III rígido unidades de disco com a [ferramenta de importação/exportação do Azure]
4. Criar uma tarefa de importação a utilizar a importação do Azure e o serviço de exportar fornecendo os ficheiros de diário produzidos pela [ferramenta de importação/exportação do Azure]
5. Envie os discos seu centro de dados do Azure indicado
6. Os dados são transferidos para o seu contentor de armazenamento de Blobs do Azure
7. Carregar os dados para o SQL dw através do PolyBase

### <a name="azcopyazcopy-utility"></a>[AZCopy][AZCopy] utilitário
O [AZCopy][AZCopy] utilitário é uma ótima ferramenta para obter os seus dados transferidos para os Blobs de armazenamento do Azure. Foi concebida para pequenas (MB C++) muito grandes (GB C++) transferências de dados. [AZCopy] também foi projetado para fornecer bom débito resiliente ao transferir dados para o Azure e, portanto, é uma ótima opção para a etapa de transferência de dados. Uma vez transferidos, pode carregar os dados com o PolyBase no SQL Data Warehouse. Também pode incorporar AZCopy seus pacotes do SSIS através de uma tarefa de "A executar o processo".

Utilizar o AZCopy primeiro terá de transferir e instalá-lo. Há uma [versão de produção] [ production version] e um [versão de pré-visualização] [ preview version] disponíveis.

Para carregar um ficheiro do seu sistema de arquivos terá um comando como o mostrado abaixo:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Um resumo do processo de alto nível seria:

1. Configurar um contentor de BLOBs de armazenamento do Azure para receber os dados
2. Exportar os dados para o armazenamento local
3. AZCopy seus dados no contentor de armazenamento de Blobs do Azure
4. Carregar os dados para o SQL Data Warehouse com o PolyBase

Documentação do total disponível: [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Otimização de dados de exportação
Além de garantir que a exportação está em conformidade com os requisitos apresentados pelo PolyBase podem também buscam otimizar a exportação de dados para melhorar ainda mais o processo.



### <a name="data-compression"></a>Compressão de dados
O PolyBase pode ler dados comprimidos gzip. Se é possível compactar seus dados para ficheiros gzip, em seguida, irá minimizar a quantidade de dados que está sendo transmitidos através da rede.

### <a name="multiple-files"></a>Vários ficheiros
Dividir grandes tabelas em vários arquivos não só ajuda a melhorar a velocidade de exportação, ele também ajuda re-startability de transferência e a capacidade de gerenciamento geral dos dados uma vez no armazenamento de Blobs do Azure. Um dos muitos recursos interessantes de PolyBase é que ele irá ler todos os ficheiros dentro de uma pasta e tratá-lo como uma tabela. Portanto, é uma boa idéia para isolar os ficheiros para cada tabela na sua própria pasta.

O PolyBase suporta também um recurso conhecido como "percurso da pasta de recursivo". Pode utilizar esta funcionalidade para melhorar ainda mais a organização dos seus dados exportados para melhorar a sua gestão de dados.

Para saber mais sobre como carregar dados com o PolyBase, veja [utilizar o PolyBase para carregar dados para o SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre a migração, consulte [migre a sua solução para o SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Para obter mais sugestões de desenvolvimento, consulte [descrição geral do desenvolvimento][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/load-azure-sql-data-warehouse.md 
[ADF Copy examples]: ../data-factory/quickstart-create-data-factory-dot-net.md
[development overview]: sql-data-warehouse-overview-develop.md
[migrar o seu esquema]: sql-data-warehouse-migrate-schema.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: /sql/tools/bcp-utility
[Use PolyBase to load data into SQL Data Warehouse]: load-data-wideworldimportersdw.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: https://azure.microsoft.com/services/data-factory/
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: https://azure.microsoft.com/documentation/services/expressroute/

[production version]: https://aka.ms/downloadazcopy/
[preview version]: https://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx

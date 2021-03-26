---
title: Execução da exportação invocando $export comando da Azure API para fHIR
description: Este artigo descreve como exportar dados de FHIR usando $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: cavoeg
ms.openlocfilehash: a5b3daa499546f3a30b5a4d133d77786a1916b6a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559201"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR


A funcionalidade de exportação a granel permite que os dados sejam exportados do Servidor FHIR de acordo com a [especificação FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Antes de utilizar $export, deverá certificar-se de que a API Azure para FHIR está configurada para a utilizar. Para configurar as definições de exportação e criar a conta de armazenamento Azure, consulte [a página de dados de exportação configurada](configure-export-data.md).

## <a name="using-export-command"></a>Usando $ comando $export

Depois de configurar a API Azure para a exportação, pode utilizar o comando $export para exportar os dados para fora do serviço. Os dados serão armazenados na conta de armazenamento que especificou enquanto configura a exportação. Para aprender a invocar $export comando no servidor FHIR, leia a documentação sobre a [especificação de $export HL7 FHIR](https://hl7.org/Fhir/uv/bulkdata/export/index.html).


**Empregos presos em mau estado**

Em algumas situações, há um potencial para um emprego ficar preso em mau estado. Isto pode ocorrer especialmente se as permissões da conta de armazenamento não tiverem sido configuradas corretamente. Uma forma de validar se a sua exportação for bem sucedida é verificar a sua conta de armazenamento para ver se os ficheiros correspondentes do contentor (ou seja, ndjson) estão presentes. Se não estiverem presentes e não houver outros postos de trabalho para exportação, então existe a possibilidade de o atual emprego estar num mau estado. Deve cancelar o trabalho de exportação enviando um pedido de cancelamento e tente voltar a fazer fila. O nosso tempo de funcionação por defeito para uma exportação em mau estado é de 10 minutos antes de parar e mudar para um novo emprego ou voltar a tentar a exportação. 

A Azure API For FHIR suporta $export nos seguintes níveis:
* [Sistema:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export)`GET https://<<FHIR service base URL>>/$export>>`
* [Paciente:](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients)`GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Grupo de doentes*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) - A Azure API para a FHIR exporta todos os recursos relacionados, mas não exporta as características do grupo: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Quando os dados são exportados, é criado um ficheiro separado para cada tipo de recurso. Para garantir que os ficheiros exportados não se tornem muito grandes. Criamos um novo ficheiro depois de o tamanho de um único ficheiro exportado se tornar maior do que 64 MB. O resultado é que pode obter vários ficheiros para cada tipo de recurso, que será enumerado (isto é, Paciente-1.ndjson, Paciente-2.ndjson). 


> [!Note] 
> `Patient/$export` e `Group/[ID]/$export` pode exportar recursos duplicados se o recurso estiver num compartimento de mais de um recurso, ou se estiver em vários grupos.

Além disso, é apoiado o controlo do estado de exportação através do URL devolvido pelo cabeçalho de localização durante a fila, bem como o cancelamento do emprego de exportação efetivo.

### <a name="exporting-fhir-data-to-adls-gen2"></a>Exportação de dados fHIR para a ADLS Gen2

Atualmente apoiamos $export para contas de armazenamento ativadas pela ADLS Gen2, com a seguinte limitação:

- O utilizador não pode tirar partido de [espaços hierárquicos](../../storage/blobs/data-lake-storage-namespace.md)de nomes , no entanto não existe uma forma de direcionar a exportação para um subdiretório específico dentro do contentor. Só fornecemos a capacidade de direcionar um recipiente específico (onde criamos uma nova pasta para cada exportação).
- Uma vez concluída a exportação, nunca mais exportamos nada para essa pasta, uma vez que as exportações subsequentes para o mesmo contentor estarão dentro de uma pasta recém-criada.


## <a name="settings-and-parameters"></a>Definições e parâmetros

### <a name="headers"></a>Cabeçalhos
Há dois parâmetros de cabeça exigidos que devem ser definidos para $export postos de trabalho. Os valores são definidos pela [especificação atual $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Aceitar** - candidatura/fhir+json
* **Prefere** - responder-async

### <a name="query-parameters"></a>Parâmetros de consulta
A Azure API para FHIR suporta os seguintes parâmetros de consulta. Todos estes parâmetros são opcionais:

|Parâmetro de consulta        | Definido pela Especificação FHIR?    |  Description|
|------------------------|---|------------|
| \_outputFormat | Yes | Atualmente suporta três valores para alinhar com a FHIR Spec: application/fhir+ndjson, application/ndjson, ou just ndjson. Todos os postos de trabalho de exportação regressarão `ndjson` e o valor passado não tem qualquer efeito sobre o comportamento do código. |
| \_desde | Yes | Permite-lhe apenas exportar recursos que foram modificados desde o tempo fornecido |
| \_tipo | Yes | Permite especificar quais tipos de recursos serão incluídos. Por exemplo, \_ type=Paciente devolveria apenas recursos do paciente|
| \_typefilter | Yes | Para solicitar uma filtragem de grãos mais finos, pode utilizar \_ o typefilter juntamente com o parâmetro do \_ tipo. O valor do parâmetro _typeFilter é uma lista separada por vírgula de consultas de FHIR que restringem ainda mais os resultados |
| \_recipiente | No |  Especifica o contentor dentro da conta de armazenamento configurada onde os dados devem ser exportados. Se um recipiente for especificado, os dados serão exportados para esse recipiente numa nova pasta com o nome. Se o recipiente não for especificado, será exportado para um novo recipiente utilizando o tempotando e a identificação do trabalho. |

> [!Note]
> Apenas as contas de armazenamento na mesma subscrição que para a AZure API para fHIR podem ser registadas como destino para $export operações.

## <a name="secure-export-to-azure-storage"></a>Exportação Segura para armazenamento Azure

A Azure API para a FHIR apoia uma operação de exportação segura. Uma opção para executar uma exportação segura é permitir endereços IP específicos associados à Azure API para fHIR aceder à conta de armazenamento Azure. Dependendo se a conta de armazenamento está na mesma ou numa localização diferente da AZure API para FHIR, as configurações são diferentes.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Quando a conta de armazenamento Azure está em uma região diferente

**Selecione networking** da conta de armazenamento Azure a partir do portal. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Definições de rede de armazenamento Azure." lightbox="media/export-data/storage-networking.png":::
   
Selecione **Redes selecionadas**. Na secção Firewall, especifique o endereço IP na caixa **de intervalo de endereços.** Adicione gamas IP para permitir o acesso a partir da internet ou das suas redes no local. Pode encontrar o endereço IP na tabela abaixo para a região de Azure, onde é prestado o serviço Azure API para o serviço FHIR.

|**Região do Azure**         |**Endereço IP público** |
|:----------------------|:-------------------|
| Leste da Austrália       | 20.53.44.80       |
| Canadá Central       | 20.48.192.84      |
| E.U.A. Central           | 52.182.208.31     |
| E.U.A. Leste              | 20.62.128.148     |
| E.U.A. Leste 2            | 20.49.102.228     |
| Leste DOS EUA 2       | 20.39.26.254      |
| Alemanha Norte        | 51.116.51.33      |
| Alemanha Centro-Oeste | 51.116.146.216    |
| Leste do Japão           | 20.191.160.26     |
| Coreia do Sul Central        | 20.41.69.51       |
| E.U.A. Centro-Norte     | 20.49.114.188     |
| Europa do Norte         | 52.146.131.52     |
| Norte da África do Sul   | 102.133.220.197   |
| E.U.A. Centro-Sul     | 13.73.254.220     |
| Sudeste Asiático       | 23.98.108.42      |
| Suíça Norte    | 51.107.60.95      |
| Sul do Reino Unido             | 51.104.30.170     |
| Oeste do Reino Unido              | 51.137.164.94     |
| E.U.A. Centro-Oeste      | 52.150.156.44     |
| Europa Ocidental          | 20.61.98.66       |
| E.U.A. Oeste 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Quando a conta de armazenamento Azure está na mesma região

O processo de configuração é o mesmo que acima, exceto uma gama específica de endereços IP no formato CIDR é usado em vez disso, 100.64.0.0/10. A razão pela qual a gama de endereços IP, que inclui 100.64.0.0 – 100.127.255.255, deve ser especificada é porque o endereço IP real utilizado pelo serviço varia, mas estará dentro do intervalo, para cada pedido de $export.

> [!Note] 
> É possível que um endereço IP privado dentro do intervalo de 10.0.2.0/24 possa ser utilizado em vez disso. Nesse caso, a operação $export não será bem sucedida. Pode voltar a tentar o pedido de $export, mas não há garantias de que um endereço IP dentro do intervalo de 100.64.0.0/10 será usado da próxima vez. É o comportamento conhecido em rede por design. A alternativa é configurar a conta de armazenamento numa região diferente.
    
## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a exportar recursos FHIR usando $export comando. Em seguida, para aprender a exportar dados des identificados, consulte:
 
>[!div class="nextstepaction"]
>[Exportação de dados não identificados](de-identified-export.md)

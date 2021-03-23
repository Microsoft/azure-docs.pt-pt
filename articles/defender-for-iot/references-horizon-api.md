---
title: API do Horizon
description: Este guia descreve métodos horizon usados comumente.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786792"
---
# <a name="horizon-api"></a>API do Horizon 

Este guia descreve métodos horizon usados comumente.

### <a name="getting-more-information"></a>Obter mais informações

Para obter mais informações sobre o trabalho com a Horizon e a plataforma Defender for IoT, consulte as seguintes informações:

- Para o Horizonte Open Development Environment (ODE) SDK, contacte o seu Representante Defender para ioT.
- Para obter informações de apoio e resolução de problemas, contacte <support@cyberx-labs.com> .

- Para aceder ao guia de utilizador Defender para IoT a partir da consola Defender para IoT, selecione :::image type="icon" source="media/references-horizon-api/profile.png"::: Download **User Guide**.


## `horizon::protocol::BaseParser`

Resumo para todos os plugins. Trata-se de dois métodos:

- Para o processamento de filtros plugin definidos acima de si. Desta forma horizon sabe como comunicar com o parser.
- Para o processamento dos dados reais.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

A primeira função que é chamada para o seu plugin cria uma instância do parser para a Horizon reconhecê-lo e registá-lo.

### <a name="parameters"></a>Parâmetros 

Nenhum.

### <a name="return-value"></a>Valor devolvido

shared_ptr à sua instância de parser.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Esta função será chamada para cada plugin registado acima. 

Na maioria dos casos, isto estará vazio. Abriu uma exceção para a Horizon saber que algo de mau aconteceu.

### <a name="parameters"></a>Parâmetros 

- Um mapa que contém a estrutura do dissect_as, tal como definido no config.jsde outro plugin que quer registar-se sobre si.

### <a name="return-value"></a>Valor devolvido 

Uma série de uint64_t, que é o registo transformado numa espécie de uint64_t. Isto significa que no mapa, você terá uma lista de portos, cujos valores serão os uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

A função principal. Especificamente, a lógica do plugin, cada vez que um novo pacote atinge o seu parser. Esta função será chamada, tudo o que está relacionado com o processamento de pacotes deve ser feito aqui.

### <a name="considerations"></a>Considerações

O seu plugin deve ser seguro para os fios, uma vez que esta função pode ser chamada de diferentes fios. Uma boa abordagem seria definir tudo na pilha.

### <a name="parameters"></a>Parâmetros

- A unidade de controlo SDK responsável por armazenar os dados e criar objetos relacionados com SDK, como iLayer, e campos.
- Um ajudante para ler os dados do pacote bruto. Já está definido com a ordem de byte que definiu no config.js.

### <a name="return-value"></a>Valor devolvido 

O resultado do processamento. Isto pode ser *sucesso,* *malforme,* ou *Sanidade.*

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Marca o processamento como falha de saneamento, o que significa que o pacote não é reconhecido pelo protocolo atual, e horizonte deve passá-lo para outro parser, se algum registrado nos mesmos filtros.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Construtor

### <a name="parameters"></a>Parâmetros 

- Define o código de erro utilizado pelo Horizonte para registo de registo, conforme definido no config.jsligado.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Resultado mal formado, indicou que já reconhecemos o pacote como o nosso protocolo, mas alguma validação correu mal (as partes reservadas estão em jogo, ou falta algum campo).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Construtor

### <a name="parameters"></a>Parâmetros  

- Código de erro, tal como definido na config.js.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Notifica horizonte de processamento bem sucedido. Quando bem sucedido, o pacote foi aceite, os dados pertencem-nos, e todos os dados foram extraídos.

## `horizon::protocol::SuccessResult()`

Construtor. Criei um resultado básico de sucesso. Isto significa que não sabemos a direção ou qualquer outro metadados sobre o pacote.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Construtor.

### <a name="parameters"></a>Parâmetros 

- A direção do pacote, se identificado. Os valores podem ser *REQUEST,* ou *RESPOSTA.*

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Construtor.

### <a name="parameters"></a>Parâmetros

- A direção do pacote, se o identificamos, pode ser *REQUEST,* *RESPONSE*.
- Avisos. Estes eventos não serão falhados, mas horizonte será notificado.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Construtor.

### <a name="parameters"></a>Parâmetros 

-  Avisos. Estes eventos não serão falhados, mas horizonte será notificado.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Converte uma referência baseada em cordas a um nome de campo (por exemplo, function_code) ao HorizonID.

### <a name="parameters"></a>Parâmetros 

- Corda para converter.

### <a name="return-value"></a>Valor devolvido

- HorizonID criado a partir da corda.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Cria uma nova camada para que a Horizon saiba que o plugin quer armazenar alguns dados. Esta é a unidade de armazenamento base que deve utilizar.

### <a name="return-value"></a>Valor devolvido

Uma referência a uma camada criada, para que possa adicionar dados a ela.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Obtém o objeto de gestão de campo, que é responsável pela criação de campos em diferentes objetos, por exemplo, no ILayer.

### <a name="return-value"></a>Valor devolvido

Uma referência ao gerente.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Cria um novo campo numérico de 64 bits na camada com o ID solicitado.

### <a name="parameters"></a>Parâmetros 

- A camada que criaste mais cedo.
- HorizonID criado pela **macro HORIZON_FIELD.**
- O valor bruto que quer armazenar.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Cria um novo campo de cordas na camada com o ID solicitado. A memória será movida, por isso tenha cuidado. Não poderá usar este valor de novo.

### <a name="parameters"></a>Parâmetros  

- A camada que criaste mais cedo.
- HorizonID criado pela **macro HORIZON_FIELD.**
- O valor bruto que quer armazenar.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Cria um novo campo de valor bruto (matriz de bytes) na camada, com o ID solicitado. A memória será mover-se, por isso tenha cuidado, não poderá voltar a usar este valor.

### <a name="parameters"></a>Parâmetros

- A camada que criaste mais cedo.
- HorizonID criado pela **macro HORIZON_FIELD.**
- O valor bruto que quer armazenar.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Cria um campo de valor de matriz (matriz) na camada do tipo especificado com o ID solicitado.

### <a name="parameters"></a>Parâmetros

- A camada que criaste mais cedo.
- HorizonID criado pela **macro HORIZON_FIELD.**
- O tipo de valores que serão armazenados dentro da matriz.

### <a name="return-value"></a>Valor devolvido

Referência a uma matriz a que deve anexar valores.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Anexa um novo valor inteiro à matriz criada anteriormente.

### <a name="parameters"></a>Parâmetros

- A matriz criada anteriormente.
- O valor bruto a ser armazenado na matriz.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Anexa um novo valor de cadeia à matriz criada anteriormente. A memória será mover-se, por isso tenha cuidado, não poderá voltar a usar este valor.

### <a name="parameters"></a>Parâmetros

- A matriz criada anteriormente.
- Valor bruto a ser armazenado na matriz.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Anexa um novo valor bruto à matriz criada anteriormente. A memória será mover-se, por isso tenha cuidado, não poderá voltar a usar este valor.

### <a name="parameters"></a>Parâmetros

- A matriz criada anteriormente.
- Valor bruto a ser armazenado na matriz.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Verifica se o tampão contém pelo menos x bytes.

### <a name="parameters"></a>Parâmetros

O número de bytes que devem existir.

### <a name="return-value"></a>Valor devolvido

É verdade se o tampão contiver pelo menos x bytes. Caso contrário, `False` é.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Lê o valor uint8 (1 byte), a partir do tampão, de acordo com a ordem byte.

### <a name="return-value"></a>Valor devolvido

O valor lido a partir do tampão.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Lê o valor uint16 (2 bytes), a partir do tampão, de acordo com a ordem byte.

### <a name="return-value"></a>Valor devolvido

O valor lido a partir do tampão.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Lê o valor uint32 (4 bytes) do tampão de acordo com a ordem byte.

### <a name="return-value"></a>Valor devolvido

O valor lido a partir do tampão.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Lê o valor uint64 (8 bytes), a partir do tampão, de acordo com a ordem byte.

### <a name="return-value"></a>Valor devolvido

O valor lido a partir do tampão.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

As leituras na memória pré-atribuída, de tamanho especificado, irão realmente copiar os dados para a sua região de memória.

### <a name="parameters"></a>Parâmetros 

- A região da memória para copiar os dados.
- Tamanho da região da memória, este parâmetro também definiu quantos bytes serão copiados.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Lê numa corda do tampão.

### <a name="parameters"></a>Parâmetros 

- O número de bytes que devem ser lidos.

### <a name="return-value"></a>Valor devolvido

A referência à região da memória da corda.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Diz-lhe quantos bytes restam no tampão.

### <a name="return-value"></a>Valor devolvido

Tamanho restante do tampão.

## `void horizon::general::IDataBuffer::skip(size_t)`

Salta x bytes no tampão.

### <a name="parameters"></a>Parâmetros

- Número de bytes para saltar.

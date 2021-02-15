---
title: SDK do Horizon
titleSuffix: Azure Defender for IoT
description: O Horizonte SDK permite que o Azure Defender para desenvolvedores IoT desenhem plugins de difusão que descodificem o tráfego da rede para que possa ser processado por programas automatizados de análise de rede Defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 06663e12bbcaee2243be75d6aa9ea9cf4fd125bf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523367"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Dissector de protocolo proprietário horizonte

Horizonte é um Ambiente de Desenvolvimento Aberto (ODE) usado para proteger dispositivos IoT e ICS executando protocolos proprietários.

Este ambiente fornece as seguintes soluções para clientes e parceiros tecnológicos:

- Suporte ilimitado e completo para protocolos comuns, proprietários, personalizados ou protocolos que se desviam de qualquer padrão. 

- Um novo nível de flexibilidade e margem de manobra para o desenvolvimento do IPC.

- Uma ferramenta que expande exponencialmente a visibilidade e o controlo da OT, sem a necessidade de atualizar o Defender para versões da plataforma IoT.

- A segurança de permitir o desenvolvimento proprietário sem divulgar informações sensíveis.

O Horizonte SDK permite que o Azure Defender para desenvolvedores IoT desenhem plugins de difusão que descodificem o tráfego da rede para que possa ser processado por programas automatizados de análise de rede Defender para IoT.

Os dissectores protocolares são desenvolvidos como plugins externos e estão integrados com uma vasta gama de serviços Defender para ioT. Por exemplo, serviços que fornecem capacidades de monitorização, alerta e reporte.

## <a name="secure-development-environment"></a>Ambiente de desenvolvimento seguro 

O ODE Horizonte permite o desenvolvimento de protocolos personalizados ou proprietários que não podem ser partilhados fora de uma organização. Por exemplo, por causa de regulamentos legais ou políticas corporativas.

Desenvolver plugins de dissitores de dissitores sem: 

- revelando qualquer informação proprietária sobre como os seus protocolos são definidos.

- partilha de qualquer um dos seus PCAPs sensíveis.

- violando os regulamentos de conformidade.

## <a name="customization-and-localization"></a>Personalização e localização  

O SDK suporta várias opções de personalização, incluindo:

  - Texto para códigos de função. 

  - Texto de localização completo para alertas, eventos e parâmetros protocolares. Para obter mais informações, consulte [Criar ficheiros de mapeamento (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Veja os alertas totalmente localizados.":::

## <a name="horizon-architecture"></a>Arquitetura horizonte

O modelo arquitetónico inclui três camadas de produto.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender para camada de plataforma IoT

Permite a integração imediata e monitorização em tempo real de plugins de dissector personalizados na plataforma Defender para IoT, sem a necessidade de atualizar a versão do Defender para a plataforma IoT.

## <a name="defender-for-iot-services-layer"></a>Defender para camada de serviços IoT

Cada serviço é projetado como um oleoduto, dissociado de um protocolo específico, permitindo um desenvolvimento mais eficiente e independente.

Cada serviço é projetado como um oleoduto, dissociado de um protocolo específico. Os serviços ouvem o tráfego no oleoduto. Interagem com os dados do plugin e com o tráfego capturado pelos sensores para indexar protocolos implantados e analisar a carga útil do tráfego, e permitir um desenvolvimento mais eficiente e independente.

## <a name="custom-dissector-layer"></a>Camada de disctor personalizado 

Permite a criação de plugins utilizando o Defender para ODK proprietário IoT (incluindo a implementação C++ e a configuração JSON) para: 

- Definir como identificar o protocolo

- Defina como mapear os campos que pretende extrair do tráfego e extraí-los 

- Definir como integrar-se com o Defender para serviços IoT

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="As camadas embuti-in.":::

O Defender for IoT fornece dissectores básicos para protocolos comuns. Podes construir os teus dissectores em cima destes protocolos.

## <a name="before-you-begin"></a>Antes de começar

## <a name="what-this-sdk-contains"></a>O que este SDK contém 

Este kit contém os ficheiros de cabeçalho necessários para o desenvolvimento. O processo de desenvolvimento requer etapas básicas e passos avançados opcionais, descritos neste SDK.

Contacte <support@cyberx-labs.com> para obter informações sobre a receção de ficheiros de cabeçalho e outros recursos.

## <a name="about-the-environment-and-setup"></a>Sobre o ambiente e a configuração 

### <a name="requirements"></a>Requisitos 

- O ambiente de desenvolvimento preferido é o Linux. Se estiver a desenvolver-se num ambiente Windows, considere utilizar um VM com um Sistema Linux.

- Para o processo de compilação, utilize O GCC 7.4.0 ou superior. Utilize qualquer classe padrão de stdlib que seja suportada em C++17.

- Defender para a versão IoT 3.0 ou superior.

### <a name="process"></a>Processo

1. [Descarregue](https://www.eclipse.org/) o Eclipse IDE para Desenvolvedores C/C++. Pode utilizar qualquer outro IDE que prefira. Este documento guia-o através da configuração utilizando o Eclipse IDE.

1. Depois de lançar o Eclipse IDE e configurar o espaço de trabalho (onde os seus projetos serão armazenados), prima **Ctrl + n,** e crie-o como um projeto C++.

1. No ecrã seguinte, desa um nome ao protocolo que pretende desenvolver e selecione o tipo de projeto como `Shared Library` e `AND Linux GCC` .

1. Editar as propriedades do projeto, em **C/C++**  >  **Configurações de configurações de** construção  >    >  **Configurações de ferramentaS GCC C++ Compilador**  >  **Miscellaneous**  >  **Tick Position Código Independente**.

1. Cole os códigos de exemplo que recebeu com o SDK e compile-os.

1. Adicione os artefactos (biblioteca, config.jse metadados) a um ficheiro .gz alcatrão e altere a extensão do ficheiro para \<XXX> .hdp, onde \<XXX> está o nome do plugin.

### <a name="research"></a>Investigação 

Antes de começar, verifique se:

- Leia a especificação do protocolo, se disponível.

- Saiba quais os campos de protocolo que pretende extrair.

- Planeiu os seus objetivos de mapeamento.

## <a name="about-plugin-files"></a>Sobre ficheiros plugin 

Três ficheiros são definidos durante o processo de desenvolvimento.

### <a name="json-configuration-file-required"></a>Ficheiro de configuração JSON (obrigatório) 

Este ficheiro deve definir o ID do dissector e as declarações, dependências, requisitos de integração, parâmetros de validação e definições de mapeamento para traduzir valores para nomes, números para texto. Para mais informações, consulte os seguintes links:

- [Preparar o ficheiro de configuração (JSON)](#prepare-the-configuration-file-json)

- [Preparar validações de código de implementação](#prepare-implementation-code-validations)

- [Metadados de dispositivo de extração](#extract-device-metadata)

- [Ligar a um serviço de indexação (Linha de Base)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Código de implementação: C++ (obrigatório)

O Código de Implementação (CPP) analisa o tráfego bruto e mapeia-o para valores como serviços, classes e códigos de função. Extrai os campos de camadas e mapeia-os para os seus nomes de índice a partir dos ficheiros de configuração JSON. Os campos a extrair do CPP são definidos em ficheiro config. para obter mais informações, consulte [Preparar o código de implementação (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Ficheiros de mapeamento (opcional)

Pode personalizar o texto de saída plugin para atender às necessidades do seu ambiente empresarial.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migração":::

Pode definir e atualizar ficheiros de mapeamento para atualizar texto sem alterar o código. Cada ficheiro pode mapear um ou muitos campos:

  - Mapeamento dos valores do campo para nomes, por exemplo, 1:Reset, 2:Início, 3:Parar.

  - Mapeamento de texto para suportar vários idiomas.

Para obter mais informações, consulte [Criar ficheiros de mapeamento (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Criar um plugin de dissector (visão geral)

1. Reveja a secção [Sobre o ambiente e a secção de configuração.](#about-the-environment-and-setup)

2.  [Preparar o código de implementação (C++)](#prepare-the-implementation-code-c). Copie o **template.jsno** ficheiro e edite-o para satisfazer as suas necessidades. Não mude as chaves. 

3. [Prepare o ficheiro de configuração (JSON)](#prepare-the-configuration-file-json). Copie o **ficheiro .cpp modelo** e implemente um método de substituição. Para mais informações, consulte [horizonte::p rotocol::BaseParser](#horizonprotocolbaseparser) para mais detalhes.

4. [Preparar validações de código de implementação](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Preparar o código de implementação (C++)

O ficheiro CPP é um analisador responsável por:

- Validação do cabeçalho do pacote e da carga útil (por exemplo, comprimento do cabeçalho ou estrutura de carga útil).

- Extrair dados do cabeçalho e carga útil em campos definidos.

- Implementação de campos configurados extração pelo ficheiro JSON.

### <a name="what-to-do"></a>O que fazer

Copie o **.cpp** ficheiro do modelo e implemente um método de substituição. Para mais informações, consulte [horizonte::p rotocol::BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Amostra básica do modelo C++ 

Esta secção fornece o modelo de protocolo básico, com funções padrão para uma amostra Defender para o Protocolo IoT Horizon.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Descrição básica do modelo C++  

Esta secção fornece o modelo de protocolo básico, com uma descrição das funções padrão para uma amostra Defender para o Protocolo IoT Horizon. 

### <a name="include-pluginpluginh"></a>#include "plugin/plugin.h"

A definição que o plugin usa. O ficheiro do cabeçalho contém tudo o que é necessário para completar o desenvolvimento.

### <a name="horizonprotocolbaseparser"></a>horizonte::p rotocol::BaseParser

A interface de comunicação entre a infraestrutura Horizon e a camada Plugin. Para mais informações, consulte [a arquitetura Horizon](#horizon-architecture) para uma visão geral sobre as camadas.

O processLayer é o método usado para processar dados.

- O primeiro parâmetro no código de função é o utilitário de processamento utilizado para recuperar dados previamente processados, criando novos campos e camadas.

- O segundo parâmetro no código de função é os dados atuais transmitidos a partir do parser anterior.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Use para criar a instância do seu parser.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Amostra de código de função do protocolo 

Esta secção fornece um exemplo de como o número de código (2 bytes) e o comprimento da mensagem (4 bytes) são extraídos.

Isto é feito de acordo com a endianness fornecida no ficheiro de configuração JSON, o que significa que se o protocolo for *pouco endianness*– e o sensor funcionar numa máquina com pouca endiança, será convertido.

Uma camada também é criada para armazenar dados. Utilize os *camposManager* a partir dos utensílios de processamento para criar novos campos. Um campo pode ter apenas um dos seguintes tipos: *STRING,* *NUMBER,* *RAW DATA,* *ARRAY* (de tipo específico) ou *COMPLEX*. Esta camada pode conter um número, cru ou corda com ID.

Na amostra a seguir, são extraídos os dois campos seguintes:

- `function_code_number`

- `headerLength`

Uma nova camada é criada, e o campo extraído é copiado nele.

A amostra abaixo descreve uma função específica, que é a principal lógica implementada para o processamento de plugins.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Campo JSON relacionado 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="O campo json relacionado.":::

## <a name="prepare-the-configuration-file-json"></a>Preparar o ficheiro de configuração (JSON)

O Horizon SDK utiliza a Notação Padrão do Objeto JavaScript (JSON), um formato leve para armazenar e transportar dados e não requer linguagens de script proprietárias.

Esta secção descreve declarações mínimas de configuração JSON, a estrutura relacionada e fornece um ficheiro de configuração de amostra que define um protocolo. Este protocolo encontra-se automaticamente integrado ao serviço de descoberta de dispositivos.

## <a name="file-structure"></a>Estrutura de ficheiros

A amostra abaixo descreve a estrutura do ficheiro.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="A amostra da estrutura do ficheiro.":::

### <a name="what-to-do"></a>O que fazer

Copie o ficheiro do modelo `config.json` e edite-o para satisfazer as suas necessidades. Não mude a chave. As teclas estão marcadas a vermelho no [ficheiro de configuração Sample JSON](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Requisitos de nomeação de ficheiros 

O ficheiro de configuração JSON deve ser guardado como `config.json` .

### <a name="json-configuration-file-fields"></a>Campos de ficheiros de configuração JSON

Esta secção descreve os campos de configuração JSON que irá definir. Não altere as *etiquetas* dos campos.

### <a name="basic-parameters"></a>Parâmetros básicos

Esta secção descreve parâmetros básicos.

| Etiqueta de parâmetro | Descrição | Tipo |
|--|--|--|
| **ID** | O nome do protocolo. Elimine o padrão e adicione o nome do seu protocolo tal como aparece. | String |
| **endianess** | Define como os dados multi byte são codificados. Use o termo "pequeno" ou "grande". Retirado da especificação do protocolo ou da gravação de tráfego | String |
| **sanity_failure_codes** | Estes são os códigos devolvidos do parser quando há um conflito de sanidade no que diz respeito à identidade do código. Consulte a validação do número mágico na secção C++. | String |
| **malformed_codes** | Estes são códigos que foram devidamente identificados, mas um erro é detetado. Por exemplo, se o comprimento do campo for demasiado curto ou longo, ou se um valor for inválido. | String |
| **dissect_as** | Uma matriz que define onde deve chegar o tráfego específico do protocolo. | TCP/UDP, porta etc. |
| **campos** | A declaração de quais os campos serão extraídos do tráfego. Cada campo tem o seu próprio ID (nome), e tipo (numérico, corda, cru, matriz, complexo). Por exemplo, a [função](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) de campo que é extraída no ficheiro Implementation Parser. Os campos escritos no ficheiro config são os únicos que podem ser adicionados à camada. |  |

### <a name="other-advanced-fields"></a>Outros campos avançados 

Esta secção descreve outros campos.

| Etiqueta de parâmetro | Description |
|-----------------|--------|
| **permitir listas** | Pode indexar os valores do protocolo e exibi-los em Relatórios de Mineração de Dados. Estes relatórios refletem a sua linha de base de rede. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Uma amostra da visão da mineração de dados."::: <br /> Para obter mais informações, consulte [Connect to a indexing service (Baseline)](#connect-to-an-indexing-service-baseline) para obter mais informações. |
| **firmware** | Pode extrair informações de firmware, definir valores de índice e acionar alertas de firmware para o protocolo plugin. Para obter mais informações, consulte [extrair dados do firmware](#extract-firmware-data) para obter mais informações. |
| **value_mapping** | Pode personalizar o texto de saída plugin para atender às necessidades do seu ambiente empresarial definindo e atualizando ficheiros de mapeamento. Por exemplo, mapear para ficheiros linguísticos. As alterações podem ser facilmente implementadas para texto sem alterar ou impactar o código. Para obter mais informações, consulte [Criar ficheiros de mapeamento (JSON)](#create-mapping-files-json) para obter mais detalhes. |

## <a name="sample-json-configuration-file"></a>Ficheiro de configuração JSON de amostra

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Preparar validações de código de implementação

Esta secção descreve funções de validação de código C++ de implementação e fornece código de amostra. Estão disponíveis duas camadas de validação:

- A sanidade.

- Código mal formado.

Não é necessário criar código de validação para construir um plugin funcional. Se não preparar o código de validação, pode rever os relatórios de data mining do sensor como uma indicação de um processamento bem sucedido.

Os valores de campo podem ser mapeados para o texto em ficheiros de mapeamento e atualizados sem impacto no processamento.

## <a name="sanity-code-validations"></a>Validações de código sanidade 

Isto valida que o pacote transmitido corresponde aos parâmetros de validação do protocolo, o que o ajuda a identificar o protocolo dentro do tráfego.

Por exemplo, utilize os primeiros 8 bytes como *número mágico*. Se a sanidade falhar, uma resposta de falha de sanidade é devolvida.

Por exemplo:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Se outros plugins relevantes tiverem sido implantados, o pacote será validado contra eles.

## <a name="malformed-code-validations"></a>Validações de código mal formadas 

As validações mal formadas são utilizadas após a validação positiva do protocolo.

Se houver uma falha no processo dos pacotes com base no protocolo, uma resposta de falha é devolvida.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="código mal formado":::

## <a name="c-sample-with-validations"></a>Amostra C++ com validações

De acordo com a função, o processo é realizado, como mostra o exemplo abaixo.

### <a name="function-20"></a>Função 20 

- É processado como firmware.

- Os campos são lidos de acordo com a função.

- Os campos são adicionados à camada.

### <a name="function-10"></a>Função 10 

- A função contém outra subfunção, que é uma operação mais específica

- A subfunção é lida e adicionada à camada.

Uma vez feito isto, o processamento está terminado. O valor de devolução indica se a camada do dissector foi processada com sucesso. Se fosse, a camada torna-se utilizável.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Metadados de dispositivo de extração

Pode extrair os seguintes metadados em ativos:

  - `Is_distributed_control_system` - Indica se o protocolo faz parte do Sistema de Controlo Distribuído. (exemplo: O protocolo SCADA deve ser falso)

  - `Has_protocol_address` – Indica se existe um endereço de protocolo; o endereço específico para o protocolo atual, por exemplo, identificador de unidade MODBUS

  - `Is_scada_protocol` - Indica se o protocolo é específico das redes OT

  - `Is_router_potential` - Indica se o protocolo é utilizado principalmente por routers. Por exemplo, LLDP, CDP ou STP.

Para tal, o ficheiro de configuração JSON precisa de ser atualizado utilizando a propriedade dos metadados.

## <a name="json-sample-with-metadata"></a>Amostra JSON com metadados 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Código de programação extrato 

Quando ocorrer um evento de programação, pode extrair o conteúdo do código. O conteúdo extraído permite-lhe:

- Compare o conteúdo do ficheiro de código em diferentes eventos de programação.

- Desencadeie um alerta sobre a programação não autorizada.  

- Desencadeie um evento para receber o ficheiro de código de programação.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="O registo de mudança de programação.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Ver a programação clicando no botão.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="O alerta de programação não autorizado da PLC.":::

Para isso, o ficheiro de configuração JSON precisa de ser atualizado utilizando a `code_extraction` propriedade. 

### <a name="json-configuration-fields"></a>Campos de configuração JSON 

Esta secção descreve os campos de configuração JSON. 

- **método**

  Indica a forma como os ficheiros de eventos de programação são recebidos. 

  ALL (cada ação de programação fará com que todos os ficheiros de código sejam recebidos mesmo que existam ficheiros sem alterações).

- **file_type**  

  Indica o tipo de conteúdo de código.  

  TEXTO (cada ficheiro de código contém informações texuais).

- **code_data_field**
  
  Indica o campo de implementação a utilizar para fornecer o conteúdo do código.

  O CAMPO.

- **code_name_field**

  Indica o campo de implementação a utilizar para fornecer o nome do ficheiro de codificação.

  O CAMPO.

- **size_limit**

  Indica o limite de tamanho de cada conteúdo de ficheiro de codificação em BYTES, se um ficheiro de código exceder o limite definido, será eliminado. Se este campo não for especificado, o valor padrão será de 15.000.000 que é de 15 MB.

  O número.

- **metadados**

  Indica informações adicionais para um ficheiro de código.

  Matriz contendo objetos com duas propriedades:

    - nome (String) -Indica que a tecla de metadados XSense suporta atualmente apenas a tecla username.
 
    - valor (Campo) - Indica o campo de implementação a utilizar para fornecer os dados de metadados.

## <a name="json-sample-with-programming-code"></a>Amostra JSON com código de programação

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Alertas de horizonte personalizado

Alguns protocolos funcionam código pode indicar um erro. Por exemplo, se o protocolo controlar um recipiente com um produto químico específico que deve ser armazenado sempre a uma temperatura específica. Neste caso, pode haver um código de função que indique um erro no termómetro. Por exemplo, se o código de função for 25, pode desencadear um alerta na Consola Web que indica que há um problema com o recipiente. Nesse caso, pode definir alertas de pacotes profundos.

Adicione o parâmetro **de alerta** ao `config.json` plugin.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Campos de configuração JSON

Esta secção descreve os campos de configuração JSON. 

| Nome do campo | Description | Valores possíveis |
|--|--|--|
| **ID** | Representa uma única identificação de alerta. Deve ser único neste contexto. | Valor numérico 0 - 10000 |
| **Mensagem** | Informação apresentada ao utilizador. Este campo permite-lhe utilizar diferentes campos. | Utilize qualquer campo a partir do seu protocolo ou qualquer protocolo de camada inferior. |
| **título** | O título de alerta |  |
| **expression** | Quando quiser que este alerta apareça. | Utilize qualquer campo numérico encontrado em camadas inferiores ou na camada atual.</br></br> Cada campo deve ser embrulhado `{}` com, para que o SDK detetá-lo como um campo, os operadores lógicos apoiados são</br> == - Igual</br> <= - Menos ou igual</br> >= - Mais do que igual</br> > - Mais do que</br> < - Menos de</br> ~= - Não é igual |

## <a name="more-about-expressions"></a>Mais sobre expressões

Sempre que o SDK Horizon invoca a expressão e se torna *verdade,* um alerta será acionado no sensor.

Expressões múltiplas podem ser incluídas sob o mesmo alerta. Por exemplo,

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Esta expressão valida o código de função apenas quando o pacote ipv4 src é de 10.10.10.10.10. Que é uma representação bruta do endereço IP em representação numérica.

Pode `and` utilizar, ou `or` para ligar expressões.

## <a name="json-sample-custom-horizon-alerts"></a>JSON amostra alertas de horizonte personalizado

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Ligar a um serviço de indexação (Linha de Base)

Pode indexar os valores do protocolo e exibi-los nos relatórios data mining.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Uma visão da opção de mineração de dados.":::

Estes valores podem ser posteriormente mapeados para textos específicos, por exemplo, mapeamento de números como textos ou adição de informação, em qualquer idioma.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migração":::

Para obter mais informações, consulte [Criar ficheiros de mapeamento (JSON)](#create-mapping-files-json) para obter mais detalhes.

Também pode utilizar valores de protocolos previamente analisados para extrair informações adicionais.

Por exemplo, para o valor, que é baseado em TCP, você pode usar os valores da camada IPv4. A partir desta camada pode extrair valores como a fonte do pacote e o destino.

Para isso, o ficheiro de configuração JSON precisa de ser atualizado utilizando a `whitelists` propriedade.

## <a name="allow-list-data-mining-fields"></a>Permitir campos de lista (data mining)

Estão disponíveis os seguintes campos de lista:

- nome – O nome utilizado para a indexação.

- alert_title – Um título curto e único que explica o evento.

- alert_text - Informação adicional

Podem ser adicionadas várias listas de permitis, permitindo uma flexibilidade total na indexação.

## <a name="json-sample-with-indexing"></a>Amostra JSON com indexação 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extrair dados de firmware

Pode extrair informações de firmware, definir valores de índice e acionar alertas de firmware para o protocolo plugin. Por exemplo,

- Extrair o modelo ou versão firmware. Estas informações podem ser ainda utilizadas para identificar os CVEs.

- Desencadeie um alerta quando for detetada uma nova versão do firmware.

Para isso, o ficheiro de configuração JSON precisa de ser atualizado utilizando a propriedade firmware.

## <a name="firmware-fields"></a>Campos de firmware

Esta secção descreve os campos de configuração do firmware JSON.

- **nome**
  
  Indica como o campo é apresentado na consola do sensor.

- **value**

 Indica o campo de implementação a utilizar para fornecer os dados. 

- **firmware_index:**

  Selecione uma das opções:  
  - **modelo**: O modelo do dispositivo. Permite a deteção de CVEs.
  - **série**: O número de série do dispositivo. O número de série nem sempre está disponível para todos os protocolos. Este valor é único por dispositivo.
  - **cremalheira**: Indica o identificador da cremalheira, se o dispositivo fizer parte de um cesto.
  - **Ranhura**: O identificador de ranhuras, se o dispositivo fizer parte de um cesto.  
  - **module_address**: Utilize para apresentar uma hierarquia se o módulo puder ser apresentado atrás de outro dispositivo. Aplicável em vez disso se uma combinação de ranhura de rack, que é uma apresentação mais simples.  
  - **firmware_version**: Indica a versão do dispositivo. Permite a deteção de CVEs.
  - **alert_text**: Indica texto que descreve desvios de firmware, por exemplo, alterações de versão.  
  - **index_by**: Indica os campos utilizados para identificar e indexar o dispositivo. No exemplo abaixo, o dispositivo é identificado pelo seu endereço IP. Em certos protocolos, um índice mais complexo pode ser usado. Por exemplo, se outro dispositivo estiver ligado, e souber extrair o seu caminho interno. Por exemplo, o ID da unidade MODBUS, pode ser usado como parte do índice, como uma combinação diferente de endereço IP e o identificador de unidade.
  - **firmware_fields**: Indica quais os campos que são campos de metadados do dispositivo. Neste exemplo, são utilizados os seguintes modelos, revisão e nome. Cada protocolo pode definir os seus próprios dados de firmware.

## <a name="json-sample-with-firmware"></a>Amostra JSON com firmware 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Atributos do dispositivo de extração 

Pode melhorar o dispositivo a informação disponível no Dispositivo em Inventário, Data Mining e outros relatórios.

- Nome

- Tipo

- Fornecedor

- Sistema Operativo

Para isso, o ficheiro de configuração JSON precisa de ser atualizado utilizando a propriedade **Properties.** 

Pode fazê-lo depois de escrever o plugin básico e extrair os campos necessários.

## <a name="properties-fields"></a>Campos de propriedades

Esta secção descreve os campos de configuração das propriedades JSON. 

**config_file** 

Contém o nome do ficheiro que define como processar cada chave nos `key` campos. O ficheiro config em si deve estar no formato JSON e ser incluído como parte da pasta do protocolo plugin.

Cada chave do JSON define o conjunto de ação que deve ser feito quando extrai esta chave de um pacote.

Cada chave pode ter:

- **Dados do Pacote** - Indica as propriedades que seriam atualizadas com base nos dados extraídos do pacote (o campo de implementação utilizado para fornecer esses dados).

- **Dados Estáticos** - Indica um conjunto de ações predefinidos `property-value` que devem ser atualizados.

As propriedades que podem ser configuradas neste ficheiro são: 

- **Nome** - Indica o nome do dispositivo.

- **Tipo** - Indica o tipo de dispositivo.

- **Fornecedor** - Indica o fornecedor do dispositivo.

- **Descrição** - Indica o modelo de firmware do dispositivo (menor prioridade do que "modelo").

- **sistema operativo** - Indica o sistema operativo do dispositivo.

### <a name="fields"></a>Campos

| Campo | Descrição |
|--|--|
| key | Indica a chave. |
| valor | Indica o campo de implementação a utilizar para fornecer os dados. |
| is_static_key | Indica se o `key` campo é derivado como um valor do pacote ou se é um valor predefinido. |

### <a name="working-with-static-keys-only"></a>Trabalhar apenas com chaves estáticas

Se está a trabalhar com teclas estáticas, então não tem de configurar a `config.file` . Só pode configurar o ficheiro JSON.

## <a name="json-sample-with-properties"></a>Amostra JSON com propriedades 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Criar ficheiros de mapeamento (JSON)

Pode personalizar o texto de saída plugin para atender às necessidades do seu ambiente empresarial definindo e atualizando ficheiros de mapeamento. As alterações podem ser facilmente implementadas para texto sem alterar ou impactar o código. Cada ficheiro pode mapear um ou muitos campos.

- Mapeamento dos valores do campo para nomes, por exemplo 1:Reset, 2:Início, 3:Parar.

- Mapeamento de texto para suportar vários idiomas.

Dois tipos de ficheiros de mapeamento podem ser definidos.

  - [Simples arquivo de mapeamento.](#simple-mapping-file)

  - [Ficheiro de mapeamento de dependência](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="éter net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Uma visão dos alertas não manipulados.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Uma lista de violações políticas conhecidas.":::

## <a name="file-naming-and-storage-requirements"></a>Requisitos de nomeação e armazenamento de ficheiros 

Os ficheiros de mapeamento devem ser guardados sob a pasta de metadados.

O nome do ficheiro deve coincidir com o ID do ficheiro config JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Uma amostra de um ficheiro config JSON.":::

## <a name="simple-mapping-file"></a>Arquivo de mapeamento simples 

A amostra a seguir apresenta um ficheiro JSON básico como um valor chave.

Quando cria uma lista de autorizações, contém um ou mais dos campos mapeados. O valor será convertido de um número, cadeia ou qualquer tipo, para texto formatado apresentado no ficheiro de mapeamento.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Arquivo de mapeamento de dependência 

Para indicar que o ficheiro é um ficheiro de dependência, adicione a palavra-chave `dependency` à configuração do mapeamento.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

O ficheiro contém um mapeamento entre o campo de dependência e o campo de funções. Por exemplo, entre a função e a função sub. A função sub-função muda de acordo com a função fornecida.

Na lista de admissões previamente configurada, não existe configuração de dependência, como mostrado abaixo.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

A dependência pode basear-se num valor específico ou num campo. No exemplo abaixo, baseia-se num campo. Se basear num valor, defina o valor do extrato a ser lido a partir do ficheiro de mapeamento.

No exemplo abaixo, a dependência segue para o mesmo valor do campo.

Por exemplo, na subfunção cinco, o significado é alterado com base na função.

  - Se for uma função de leitura, então cinco significa Ler Memória.

  - Se for uma função de escrita, o mesmo valor é usado para ler a partir de um ficheiro.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Ficheiro de exemplo

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Amostra de JSON com mapeamento

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Pacote, carre faça upload e monitorize o plugin 

Esta secção descreve como

  - Embale o plugin.

  - Faça o upload do plugin.

  - Monitorize e depure o plugin para avaliar o seu bom desempenho.

Para embalar o plugin:

1. Adicione o **artefacto** (pode ser, biblioteca, config.jsou metadados) a um `tar.gz` ficheiro.

1. Altere a extensão do ficheiro para \<XXX.hdp> , onde está o nome do \<XXX> plugin.

Para iniciar sinsução na Consola Horizonte:

1.  Inscreva-se no seu sensor CLI como administrador, CyberX ou Utilizador de Suporte.

2.  No ficheiro: `/var/cyberx/properties/horizon.properties` altere a **propriedade ui.ativada** para **ser verdadeira** `horizon.properties:ui.enabled=true` ().

3.  Inscreva-se na consola do sensor.

4.  Selecione a opção **Horizonte** no menu principal.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Selecione a opção horizonte a partir do painel lateral esquerdo.":::

    A Consola Horizonte abre.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Uma vista da consola Horizon e de todos os seus plugins.":::

## <a name="plugins-pane"></a>Painel de plugins

O painel plugin lista:

  - Plugins de infraestrutura: Plugins de infraestrutura instalados por defeito com o Defender para IoT.

  - Plugins de aplicação: Plugins de aplicação instalados por padrão com o Defender para IoT e outros plugins desenvolvidos pelo Defender para IoT, ou desenvolvedores externos.
    
Ative e desative os plugins que foram carregados com o toggle.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="O CIP alterna.":::

### <a name="uploading-a-plugin"></a>Carregar um plugin

Depois de criar e embalar o plugin, pode enviá-lo para o sensor Defender para IoT. Para obter a cobertura completa da sua rede, deverá fazer o upload do plugin para cada sensor da sua organização.

Para carregar:

1.  Inscreva-se no seu sensor.


2. Selecione **Carregar**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Carrego os plugins.":::

3. Navegue pelo plugin e arraste-o para a caixa de diálogo plugin. Verifique se o prefixo está `.hdp` . O plugin carrega.

## <a name="plugin-status-overview"></a>Visão geral do estado do plugin 

A janela **Overview** da consola Horizonte fornece informações sobre o plugin que carregou e permite desativá-los e capacitá-los.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="A visão geral da consola Horizon.":::

| Campo | Descrição |
|--|--|
| Aplicação | O nome do plugin que carregou. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="O interruptor ligado e desligado."::: | Ligar **ou** **desligar** o plugin. O Defender para IoT não manuseará o tráfego de protocolo definido no plugin quando você tirar o plugin. |
| Hora | A última vez que os dados foram analisados. Atualizado a cada 5 segundos. |
| PPS | O número de pacotes por segundo. |
| Largura de banda | A largura de banda média detetada nos últimos 5 segundos. |
| Malforms | As validações mal formadas são utilizadas após a validação positiva do protocolo. Se houver uma falha no processo dos pacotes com base no protocolo, uma resposta de falha é devolvida.   <br><br>Esta coluna indica o número de erros de malforme nos últimos 5 segundos. Para mais informações, consulte [as validações do código Malforme](#malformed-code-validations) para mais detalhes. |
| Avisos | Os pacotes correspondem à estrutura e especificação, mas há um comportamento inesperado baseado na configuração de aviso plugin. |
| Erros | O número de pacotes que falharam nas validações básicas do protocolo. Valida que o pacote corresponde às definições do protocolo. O Número aqui apresentado indica que o número de erros detetados nos últimos 5 segundos. Para mais informações, consulte [as validações do código Sanity](#sanity-code-validations) para mais detalhes. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="O ícone do monitor."::: | Reveja detalhes sobre malform e avisos detetados para o seu plugin. |

## <a name="plugin-details"></a>Detalhes do plugin

Pode monitorizar o comportamento do plugin em tempo real analisando o número de *Malform* e *Advertências detetadas* para o seu plugin. Uma opção está disponível para congelar o ecrã e exportar para mais investigação

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="O ecrã do monitor SNMP.":::

Para monitorizar:

Selecione o botão Monitor para o seu plugin a partir da visão geral.

## <a name="next-steps"></a>Passos seguintes

Configurar a sua [API Horizonte](references-horizon-api.md)

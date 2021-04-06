---
title: Códigos de erro do cliente para atualização de dispositivos para Azure IoT Hub | Microsoft Docs
description: Este documento fornece uma tabela de códigos de erro do cliente para vários componentes de Atualização de Dispositivos.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200345"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Atualização do dispositivo para códigos de erro do hub IoT

Este documento fornece uma tabela de códigos de erro para vários componentes de Atualização de Dispositivos. Isto destina-se a ser usado como uma referência para os utilizadores que pretendem tentar analisar os seus próprios códigos de erro para diagnosticar e resolver problemas.

Existem dois componentes primários do lado do cliente que podem lançar códigos de erro: o agente de atualização do dispositivo e o agente de otimização de entrega.

## <a name="device-update-agent"></a>Agente de atualização de dispositivos

### <a name="resultcode-and-extendedresultcode"></a>ResultadosCode e ExtendedResultCode

A Atualização do Dispositivo para relatórios de interface IoT Hub Core PnP `ResultCode` e , que podem ser `ExtendedResultCode` utilizados para diagnosticar falhas. [Saiba mais](device-update-plug-and-play.md) sobre a interface Core PnP da atualização do dispositivo.

#### <a name="resultcode"></a>Resultados Código

`ResultCode` é um código de estado geral e segue a convenção de código de estado http.
[Saiba mais](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) sobre os códigos de estado de http.

#### <a name="extendedresultcode"></a>Código deResulte estendido

`ExtendedResultCode` é um inteiro com informações de erro codificadas.

Provavelmente verá o `ExtendedResultCode` número de inteiros assinados na interface PnP. Para descodificar `ExtendedResultCode` o , converta o inteiro assinado para o hexáxinho não assinado. Apenas os primeiros 4 bytes do `ExtendedResultCode` 1º lugar são utilizados e são da forma `F` `FFFFFFF` em que o primeiro mordidela é o **Código de Facilidade** e o resto dos bits são o Código de **Erro**.

**Códigos de Facilidade**

| Código de Facilidade     | Description  |
|-------------------|--------------|
| D                 | Erro levantado do DO SDK|
| E                 | O código de erro é um errno |


Por exemplo:

`ExtendedResultCode` é `-536870781`

A representação hexa negra não assinada `-536870781` `FFFFFFFF E0000083` é.

| Ignorar    | Código de Facilidade  | Código de Erro   |
|-----------|----------------|--------------|
| FFFFFF  | E              | 0000083      |

`0x83` em hexáxia é `131` em decimal, que é o valor errno para `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Agente de otimização de entrega
A tabela que se segue lista os códigos de erro relativos ao componente de Otimização de Entrega (DO) do cliente de Atualização do Dispositivo. O componente DO é responsável por descarregar conteúdo de atualização no dispositivo IoT.

O código de erro DO pode ser obtido examinando as exceções lançadas em resposta a uma chamada da API. Todos os códigos de erro DO podem ser identificados pelo prefixo 0x80D0.

| Código de Erro  | Erro de cadeia                       | Tipo                 | Description |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | n/a                  | A Otimização de Entrega não foi capaz de fornecer o serviço |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Baixar Job         | O download de um ficheiro não viu progressos dentro do período definido |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Baixar Job         | SetProperty() ou GetProperty() chamado com um ID de propriedade desconhecida |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Baixar Job         | Não é possível ligar para o SetProperty numa propriedade só de leitura |
| 0x80D02013L | DO_E_INVALID_STATE                 | Baixar Job         | A ação solicitada não é permitida no estado de trabalho atual. O trabalho pode ter sido cancelado ou concluído a transferência. Está num estado só de leitura agora. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Baixar Job         | Não é possível iniciar um download porque não foi especificado nenhum lavatório de descarregamento (arquivo local ou interface de fluxo) |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | IDODownload Interface| O download foi iniciado sem fornecer um URI |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Condições transitórias | Download interrompido devido à perda de conectividade da rede |

## <a name="device-update-content-service"></a>Serviço de conteúdo de atualização de dispositivos
A tabela que se segue lista os códigos de erro relativos ao componente de serviço de conteúdo do serviço de atualização do dispositivo. A componente de serviço de conteúdo é responsável pelo tratamento da importação de conteúdos de atualização.

| Código de Erro                    | Erro de cadeia                                                               | Passos seguintes                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Atualização com a mesma identidade já existe.                              | Certifique-se de que está a importar uma atualização que ainda não foi importada para este caso de Device Update para IoT Hub. |
| "DuplicateContentImport"      | Conteúdo idêntico importado simultaneamente várias vezes.                  | O mesmo que para updateAlreadyExists. |
| "Não pode ProcessarImportManifest" | Manifesto de importação de processamento de erros.                                          | Consulte os [conceitos de importação](./import-concepts.md) e a documentação [de atualização de importação](./import-update.md) para uma formatação adequada do manifesto de importação. |
| "Não pode Descarregar"              | Não é possível descarregar o manifesto de importação.                                           | Verifique se o URL do ficheiro manifesto de importação ainda é válido. |
| "Não pode Parar"                 | Não pode analisar o manifesto de importação.                                              | Verifique se o seu manifesto de importação é mais exatidão face ao esquema definido na documentação de [atualização de importação.](./import-update.md) |
| "Não apoiada"          | A versão do esquema manifesto de importação não é suportada.                           | Certifique-se de que o seu manifesto de importação está a utilizar o esquema mais recente definido na documentação de [atualização de importação.](./import-update.md) |
| "UpdateLimitExceeded"         | Atualização de importação de erros devido ao limite ultrapassado.                              | Atingiu um limite no número de diferentes Fornecedores, Nomes ou Versões permitidos no seu caso de Atualização de Dispositivos para IoT Hub. Elimine algumas atualizações da sua instância e tente novamente. |
| "UpdateProvider"              | Não é possível importar um novo fornecedor de atualização.                                       | Atingiu um limite no número de __fornecedores diferentes permitidos__ no seu caso de Atualização de Dispositivo para IoT Hub. Elimine algumas atualizações da sua instância e tente novamente. |
| "Atualizar nome"                  | Não é possível importar um novo nome de atualização para o fornecedor especificado.                | Atingiu um limite no número de __nomes diferentes__ permitidos por um Fornecedor no seu exemplo de Atualização de Dispositivos para IoT Hub. Elimine algumas atualizações da sua instância e tente novamente. |
| "Actualizaçãoversão"               | Não é possível importar uma nova versão de atualização para o fornecedor e nome especificados.    | Atingiu um limite no número de __versões diferentes permitidas__ por um Fornecedor e Nome no seu exemplo de Atualização de Dispositivo para IoT Hub. Elimine algumas atualizações com esse nome da sua instância e tente novamente. |
| "Atualização Decompatabilidade" | Não é possível importar um fornecedor de atualização adicional com a compatibilidade especificada. | Ao definir as propriedades de compatibilidade do fabricante do dispositivo e do modelo do dispositivo num manifesto de importação, tenha em mente que a Atualização do Dispositivo para o IoT Hub suporta uma única combinação de Fornecedor e Nome para um determinado fabricante/modelo. Isto significa que se tentar utilizar as mesmas propriedades de compatibilidade fabricante/modelo com mais de uma combinação Fornecedor/Nome, verá estes erros. Para resolver isto, certifique-se de que todas as atualizações de um determinado dispositivo (tal como definido pelo fabricante/modelo) utilizam o mesmo Fornecedor e Nome. Embora não seja necessário, pode considerar fazer do Fornecedor o mesmo que o fabricante e o Nome o mesmo que o modelo, apenas para simplificar. |
| "Actualizaracompatbilidade"     | Não é possível importar nome de atualização adicional com a compatibilidade especificada.     | O mesmo que para UpdateProviderCompatibility.ContentLimitNamespaceCompatibility. |
| "Atualização Comparação Decompatbilidade"  | Não é possível importar versão de atualização adicional com a compatibilidade especificada.  | O mesmo que para UpdateProviderCompatibility.ContentLimitNamespaceCompatibility. |
| "Não pode ProcessarUpdateFile"     | Ficheiro de origem de processamento de erro.                                              |                                    |
| "ContentFileCannotDownload"   | Não é possível descarregar o ficheiro de origem.                                               | Verifique se o URL dos ficheiros de atualização ainda é válido. |

**[Próximo passo: Problemas de resolução de problemas com atualização do dispositivo](.\troubleshoot-device-update.md)**

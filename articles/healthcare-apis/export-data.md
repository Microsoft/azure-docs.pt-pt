---
title: Execução da exportação invocando $export comando da Azure API para fHIR
description: Este artigo descreve como configurar e usar exportação desa identificada
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 433d3391e7b2b99d72ea71f1b4d48c3e04a46afc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819980"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR

A funcionalidade de exportação a granel permite que os dados sejam exportados do Servidor FHIR de acordo com a [especificação FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Antes de utilizar $export, deverá certificar-se de que a API Azure para FHIR está configurada para a utilizar. Para configurar as definições de exportação e criar a conta de armazenamento Azure, consulte [a página de dados de exportação configurada](configure-export-data.md).

## <a name="using-export-command"></a>Usando o comando $export

Depois de configurar a API Azure para a exportação, pode utilizar o comando $export para exportar os dados para fora do serviço. Os dados serão armazenados na conta de armazenamento que especificou enquanto configura a exportação. Para aprender a invocar $export comando no servidor FHIR, leia a documentação sobre a [especificação $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

O comando $export em Azure API para FHIR tem um parâmetro de _ \_ contentor_ opcional que especifica o recipiente dentro da conta de armazenamento configurada onde os dados devem ser exportados. Se for especificado um recipiente, os dados serão exportados para esse contentor numa nova pasta com o nome do recipiente. Se não for especificado nenhum recipiente, os dados serão exportados para um novo recipiente.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Cenários suportados

A Azure API para FHIR suporta $export ao nível do sistema, paciente e grupo. Para a exportação de grupos, exportamos todos os recursos conexas, mas não exportamos as características do grupo.

> [!Note] 
> $export exportará recursos duplicados se o recurso estiver num compartimento com mais de um recurso, ou se estiver em vários grupos.

Além disso, é apoiado o controlo do estado de exportação através do URL devolvido pelo cabeçalho de localização durante a fila, bem como o cancelamento do emprego de exportação efetivo.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a exportar recursos FHIR usando $export comando. Em seguida, pode rever as nossas funcionalidades suportadas
 
>[!div class="nextstepaction"]
>[Funcionalidades suportadas](fhir-features-supported.md)

---
title: Execução da exportação invocando $export comando da Azure API para fHIR
description: Este artigo descreve como configurar e usar exportação desa identificada
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482322"
---
# <a name="how-to-export-fhir-data"></a>Como exportar dados do FHIR

Para configurar as definições de exportação e criar uma conta de armazenamento Azure, consulte [aqui](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Exportação de recursos FHIR utilizando o comando $export

Depois de configurarmos a Azure API para a exportação, podemos agora ir e usar o comando $export para exportar os dados para fora do serviço para a conta de armazenamento que especificamos enquanto configuramos a exportação. Para aprender a invocar $export comando no servidor FHIR, leia a documentação sobre $export especificação em [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

O comando $export em Azure API para FHIR tem um parâmetro _ \_ conatiner_ opcional que pode ser usado para especificar o recipiente dentro da conta de armazenamento configurada para a qual os dados devem ser exportados.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Note que atualmente a AZure API para fHIR apenas suporta a exportação de nível do sistema, tal como definida na especificação $export [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) em . Além disso, apenas _ \_ uma_ vez que o parâmetro de consulta é suportado atualmente.

## <a name="exporting-de-identified-data-preview"></a>Exportação de dados não identificados (pré-visualização)

O comando $export também pode ser usado para exportar dados desfotados do servidor FHIR. Utiliza o motor de anonimização a partir de [ferramentas FHIR para a anonimização,](https://github.com/microsoft/FHIR-Tools-for-Anonymization)e leva detalhes de config de anonimização em parâmetros de consulta. Pode criar o seu próprio ficheiro config de anonimização ou utilizar o [ficheiro config](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) da amostra para o método HIPAA Safe Harbor como ponto de partida. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Parâmetro de consulta            | Exemplo |Opcionalidade| Descrição|
|---------------------------|---------|-----------|------------|
| _\_anonimizaçãoConfig_   |DemoConfig.jsem|Necessário para exportação deses identificou |Nome do ficheiro de configuração. Consulte [aqui](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)o formato do ficheiro de configuração . Este ficheiro deve ser mantido dentro de um recipiente chamado **anonimização** dentro da mesma conta de armazenamento Azure que é configurada como o local de exportação. |
| _\_anonimizaçãoConfigEtag_|"0x8D8494A069489EC"|Opcional para exportação deses identificou|Este é o Etag do ficheiro de configuração. Você pode obter o Etag usando Azure Storage Explorer a partir da propriedade blob|

> [!IMPORTANT]
> Note-se que tanto as exportações brutas como as exportações desescoduídas escrevem para a mesma conta de armazenagem Azure especificada como parte da configuração da exportação. Recomenda-se que utilize diferentes recipientes correspondentes a diferentes config desconsintados e que gerem o acesso do utilizador ao nível do recipiente.

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a exportar recursos FHIR usando $export comando, incluindo dados desfoqueados. Em seguida, pode configurar os seus dados de exportação:
 
>[!div class="nextstepaction"]
>[configurar dados de exportação](configure-export-data.md)

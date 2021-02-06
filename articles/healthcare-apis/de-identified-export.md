---
title: Exportação de dados não identificados (pré-visualização) para a API Azure para o FHIR
description: Este artigo descreve como configurar e usar exportação desa identificada
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: matjazl
ms.openlocfilehash: 60a2a41a8005e8bd0fbc313c9a177d54df6dac5e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627168"
---
# <a name="exporting-de-identified-data-preview"></a>Exportação de dados não identificados (pré-visualização)

> [!Note] 
> Os resultados da utilização da exportação despromotida variarão em função de fatores como os dados introduzidos e funções selecionadas pelo cliente. A Microsoft não consegue avaliar as saídas de exportação despromos identificadas ou determinar a aceitabilidade dos casos de utilização do cliente e as necessidades de conformidade. A exportação desatificada não está garantida para satisfazer quaisquer requisitos legais, regulamentares ou de conformidade específicos.

O comando $export também pode ser usado para exportar dados desfotados do servidor FHIR. Utiliza o motor de anonimização a partir de [ferramentas FHIR para a anonimização,](https://github.com/microsoft/FHIR-Tools-for-Anonymization)e leva detalhes de config de anonimização em parâmetros de consulta. Pode criar o seu próprio ficheiro config de anonimização ou utilizar o [ficheiro config](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) da amostra para o método HIPAA Safe Harbor como ponto de partida. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Neste momento, a API Azure para a FHIR apenas suporta a exportação desin identificada ao nível do sistema ($export).

|Parâmetro de consulta            | Exemplo |Opcionalidade| Description|
|---------------------------|---------|-----------|------------|
| _\_anonimizaçãoConfig_   |DemoConfig.jsem|Necessário para exportação deses identificou |Nome do ficheiro de configuração. Consulte [aqui](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)o formato do ficheiro de configuração . Este ficheiro deve ser mantido dentro de um recipiente chamado **anonimização** dentro da mesma conta de armazenamento Azure que é configurada como o local de exportação. |
| _\_anonimizaçãoConfigEtag_|"0x8D8494A069489EC"|Opcional para exportação deses identificou|Este é o Etag do ficheiro de configuração. Você pode obter o Etag usando explorador de armazenamento Azure a partir da propriedade blob|

> [!IMPORTANT]
> Tanto as exportações brutas como as exportações desesta identificáveis escrevem na mesma conta de armazenagem Azure especificada como parte da configuração da exportação. Recomenda-se que utilize diferentes recipientes correspondentes a diferentes config desconsintados e que gerem o acesso do utilizador ao nível do recipiente.
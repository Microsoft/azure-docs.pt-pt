---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019152"
---
|Nome do Parâmetro| Tipo | Description| Valores Possíveis|
|-|-|-|-|
| /ServerMode|Obrigatório|Especifica se a configuração e os servidores de processos devem ser instalados, ou apenas o servidor de processos|CS<br>PS|
|/InstallLocation|Obrigatório|A pasta na qual os componentes são instalados| Qualquer pasta no computador|
|/MySQLCredsFilePath|Obrigatório|O caminho do ficheiro no qual as credenciais do servidor MySQL são armazenadas|O ficheiro deve estar no formato especificado abaixo|
|/VaultCredsFilePath|Obrigatório|O caminho do ficheiro de credenciais do cofre|Caminho do ficheiro válido|
|/EnvType|Obrigatório|Tipo de ambiente que quer proteger |VMware<br>NonVMware|
|/PSIP|Obrigatório|Endereço IP do NIC a ser utilizado para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Obrigatório|O endereço IP do NIC em que o servidor de configuração está a ouvir| Qualquer endereço IP válido|
|/PassphraseFilePath|Obrigatório|O caminho completo para a localização do ficheiro da frase de acesso|Caminho do ficheiro válido|
|/BypassProxy|Opcional|Especifica que o servidor de configuração estabelece uma ligação ao Azure sem um proxy||
|/ProxySettingsFilePath|Opcional|Definições de proxy (o proxy predefinido necessita de autenticação ou de um proxy personalizado)|O ficheiro deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|Número da porta no PSIP a ser utilizado para dados de replicação| Número de Porta Válido (o valor predefinido é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar a verificação de espaços para a cache do disco| |
|/AcceptThirdpartyEULA|Obrigatório|O sinalizador indica a aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Apresenta o EULA de terceiros. Se for fornecido como entrada, todos os outros parâmetros são ignorados| |

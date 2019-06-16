---
title: O Azure Data Box resolução de problemas para usando a interface REST | Documentos da Microsoft
description: Descreve como resolver problemas detetados no Azure Data Box quando a cópia de dados é através da interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: c5ceeb2e6419cab7945454087edd4c821db28343
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204225"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Resolver problemas relacionados com o armazenamento de BLOBs de caixa de dados do Azure

Este artigo detalhes sobre como resolver problemas poderá ver quando utilizar o armazenamento de BLOBs de caixa de dados por meio da interface REST em sua caixa de dados para copiar dados. Superfície esses problemas quando estiver a utilizar o armazenamento de BLOBs de caixa de dados com outros aplicativos ou bibliotecas de cliente, como biblioteca de Explorador de armazenamento do Azure, o AzCopy ou o armazenamento do Azure para Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erros de ver no Explorador de armazenamento do Azure

Esta secção fornece detalhes sobre alguns dos problemas enfrentados ao utilizar o Explorador de armazenamento do Azure com o armazenamento de BLOBs de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Não é possível recuperar os recursos subordinados. O valor para um dos cabeçalhos de HTTP não está no formato correto.|Partir do **edite** menu, selecione **APIs do destino do Azure Stack**. <br>Reinicie o Explorador de armazenamento do Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro de anfitriões nesse caminho: <li>`C:\Windows\System32\drivers\etc\hosts` no Windows, ou </li><li> `/etc/hosts` no Linux.</li>|
|Não é possível recuperar os recursos subordinados. <br>Detalhes: um certificado autoassinado |Importe o certificado SSL para o seu dispositivo para o Explorador de armazenamento do Azure: <li>Transfira o certificado do portal do Azure. Para obter mais informações, aceda a [transfira o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).</li><li>Partir do **edite** menu, selecione **certificados SSL** e, em seguida, selecione **importar certificados**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erros vistos na AzCopy para Windows

Esta secção fornece detalhes sobre alguns dos problemas enfrentados ao utilizar o AzCopy para Windows com o armazenamento de BLOBs de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Comando do AzCopy for apresentada a suspender durante um minuto antes de exibir este erro: <br>Falha ao enumerar directory https://... Não foi possível resolver o nome remoto `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro de anfitriões em: `C:\Windows\System32\drivers\etc\hosts`.|
|Comando do AzCopy for apresentada a suspender durante um minuto antes de exibir este erro: <br>Localização de origem de análise de erro. A ligação subjacente foi fechada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS.|Importe o certificado SSL para o seu dispositivo para o arquivo de certificados do sistema. Para obter mais informações, aceda a [transfira o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erros vistos na AzCopy para Linux

Esta secção fornece detalhes sobre alguns dos problemas enfrentados ao utilizar o AzCopy para Linux com o armazenamento de Blob de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Comando do AzCopy for apresentada a suspender durante 20 minutos antes de exibir este erro: <br>Erro ao analisar a localização de origem `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Não existe esse endereço ou o dispositivo|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro de anfitriões em: `/etc/hosts`.|
|Comando do AzCopy for apresentada a suspender durante 20 minutos antes de exibir este erro: <br>Localização de origem de análise de erro... Não foi possível estabelecer a ligação SSL.|Importe o certificado SSL para o seu dispositivo para o arquivo de certificados do sistema. Para obter mais informações, aceda a [transfira o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate).|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erros detetados na biblioteca de armazenamento do Azure para Python

Esta secção fornece detalhes sobre alguns dos principais problemas enfrentados durante a implementação de disco de caixa de dados ao utilizar um cliente Linux para a cópia de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O valor para um dos cabeçalhos de HTTP não está no formato correto. |A versão instalada da biblioteca de armazenamento do Microsoft Azure para Python não é suportada pelo Data Box. Consulte os requisitos de armazenamento de BLOBs de caixa de dados do Azure para versões suportadas.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] …|Antes de executar o Python, defina a variável de ambiente REQUESTS_CA_BUNDLE para o caminho do ficheiro de certificado SSL codificada em Base64 (consulte a [transfira o certificado](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Por exemplo:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Como alternativa, adicione o certificado ao arquivo de certificados do sistema e, em seguida, definir esta variável de ambiente para o caminho de armazenamento. <br> Por exemplo, no Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erros comuns

Estes erros não são específicos para qualquer aplicativo.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|A ligação exceder o tempo limite. |Inicie sessão no dispositivo do Data Box e verificar se é desbloqueado. Sempre que os reinícios do dispositivo e permanece bloqueado até que alguém inicia sessão.|

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [requisitos de sistema de armazenamento de BLOBs de caixa de dados](data-box-system-requirements-rest.md).

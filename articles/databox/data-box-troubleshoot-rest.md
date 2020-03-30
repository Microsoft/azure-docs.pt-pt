---
title: Tiroteio na Caixa de Dados Azure para utilização da interface REST Microsoft Docs
description: Descreve como resolver problemas vistos na Caixa de Dados Do Azure quando a cópia de dados é através da interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297133"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Problemas relacionados com armazenamento de caixas de dados do Azure

Este artigo detalha informações sobre como resolver problemas que poderá ver ao utilizar o armazenamento do Data Box Blob através da interface REST na sua Caixa de Dados para copiar dados. Estes problemas surgem quando está a utilizar o armazenamento de Caixas de Dados Blob com outras aplicações ou bibliotecas de clientes, como o Azure Storage Explorer, AzCopy ou a Biblioteca de Armazenamento Azure para Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erros vistos no Explorador de Armazenamento Azure

Esta secção detalha alguns dos problemas com que se depara minhá-lo ao utilizar o Azure Storage Explorer com o armazenamento de Caixa de Dados Blob.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Incapaz de recuperar recursos infantis. O valor de um dos cabeçalhos HTTP não está no formato correto.|A partir do menu **Editar,** selecione **Target Azure Stack APIs**. <br>Restart Azure Storage Explorer.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verifique se o `<accountname>.blob.<serialnumber>.microsoftdatabox.com` nome final é adicionado ao ficheiro dos anfitriões neste caminho: <li>`C:\Windows\System32\drivers\etc\hosts`no Windows, ou </li><li> `/etc/hosts`em Linux.</li>|
|Incapaz de recuperar recursos infantis. <br>Detalhes: certificado auto-assinado |Importar o certificado TLS/SSL para o seu dispositivo para o Explorador de Armazenamento Azure: <li>Faça o download do certificado do portal Azure. Para mais informações, vá fazer [download do certificado.](data-box-deploy-copy-data-via-rest.md#download-certificate)</li><li>A partir do menu **Editar,** selecione **Certificados SSL** e, em seguida, selecione **Certificados**de Importação .</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erros vistos no AzCopy para Windows

Esta secção detalha alguns dos problemas com que se depara ma toda a AzCopy para windows com armazenamento de Caixa de Dados Blob.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece ser pendurado durante um minuto antes de apresentar este erro: <br>Falhou em enumerar o diretório https://... O nome remoto não podia ser resolvido`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o `<accountname>.blob.<serialnumber>.microsoftdatabox.com` nome final do ponto `C:\Windows\System32\drivers\etc\hosts`final é adicionado ao ficheiro dos anfitriões em: .|
|O comando AzCopy parece ser pendurado durante um minuto antes de apresentar este erro: <br>Erro de análise da localização da origem. A ligação subjacente foi encerrada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS.|Importe o certificado TLS/SSL para o seu dispositivo no certificado do sistema. Para mais informações, vá fazer [download do certificado.](data-box-deploy-copy-data-via-rest.md#download-certificate)|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erros vistos no AzCopy para linux

Esta secção detalha alguns dos problemas com que se depara ma toda a AzCopy para o Linux com o armazenamento de Data Box Blob.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece ser pendurado durante 20 minutos antes de apresentar este erro: <br>Erro de análise `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`da localização da origem. Nenhum tal dispositivo ou endereço|Verifique se o `<accountname>.blob.<serialnumber>.microsoftdatabox.com` nome final do ponto `/etc/hosts`final é adicionado ao ficheiro dos anfitriões em: .|
|O comando AzCopy parece ser pendurado durante 20 minutos antes de apresentar este erro: <br>Erro de análise da localização da origem... A ligação SSL não pôde ser estabelecida.|Importe o certificado TLS/SSL para o seu dispositivo no certificado do sistema. Para mais informações, vá fazer [download do certificado.](data-box-deploy-copy-data-via-rest.md#download-certificate)|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erros vistos na biblioteca de armazenamento azure para Python

Esta secção detalha alguns dos principais problemas enfrentados durante a implementação do Disco de Caixa de Dados ao utilizar um cliente Linux para cópia de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O valor de um dos cabeçalhos HTTP não está no formato correto. |A versão instalada da Biblioteca de Armazenamento Microsoft Azure para Python não é suportada pela Data Box. Consulte os requisitos de armazenamento da Caixa de Dados Azure para versões suportadas.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Antes de executar python, detete a variável ambiente REQUESTS_CA_BUNDLE para o caminho do ficheiro de certificado TLS codificado pela Base64 (ver como [descarregar o certificado).](data-box-deploy-copy-data-via-rest.md#download-certificate) <br>Por exemplo:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Alternadamente, adicione o certificado à loja de certificados do sistema e, em seguida, coloque esta variável ambiental no caminho dessa loja. <br> Por exemplo, no Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erros comuns

Estes erros não são específicos de qualquer aplicação.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|A ligação está fora. |Assine o dispositivo Data Box e verifique se está desbloqueado. Sempre que o dispositivo recomeça, fica bloqueado até que alguém entre.|

## <a name="next-steps"></a>Passos seguintes

- Conheça os requisitos do sistema de [armazenamento Data Box Blob](data-box-system-requirements-rest.md).

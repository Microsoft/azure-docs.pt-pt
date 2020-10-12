---
title: Azure Data Box resolução de problemas para a utilização da interface REST Microsoft Docs
description: Descreve como resolver problemas vistos na Caixa de Dados Azure quando a cópia de dados é através da interface REST.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: b950f80ba8c2bdbaf7a515dc1ce127b934723177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85558560"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Problemas relacionados com o armazenamento de blob da caixa de dados Azure

Este artigo detalha informações sobre como resolver problemas que pode ver ao utilizar o armazenamento de Bloco de Caixa de Dados através da interface REST na sua Caixa de Dados para copiar dados. Estes problemas surgem quando está a utilizar o armazenamento de Data Box Blob com outras aplicações ou bibliotecas de clientes, tais como Azure Storage Explorer, AzCopy ou Azure Storage library for Python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Erros vistos no Explorador de Armazenamento Azure

Esta secção detalha alguns dos problemas enfrentados ao utilizar o Azure Storage Explorer com armazenamento de caixa de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|Incapaz de recuperar recursos infantis. O valor de um dos cabeçalhos HTTP não está no formato correto.|No menu **Editar,** selecione **ApIs target Azure Stack**. <br>Reinicie o Explorador de Armazenamento Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões neste caminho: <li>`C:\Windows\System32\drivers\etc\hosts` no Windows, ou </li><li> `/etc/hosts` em Linux.</li>|
|Incapaz de recuperar recursos infantis. <br>Detalhes: certificado auto-assinado |Importe o certificado TLS/SSL para o seu dispositivo no Azure Storage Explorer: <li>Faça o download do certificado do portal Azure. Para mais informações, aceda ao [Download do certificado.](data-box-deploy-copy-data-via-rest.md#download-certificate)</li><li>No menu **Editar,** selecione **Certificados SSL** e, em seguida, selecione **Certificados de Importação**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Erros vistos no AzCopy para Windows

Esta secção detalha alguns dos problemas enfrentados ao utilizar o AzCopy para Windows com armazenamento de Bolha de Caixa de Dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece parar de responder durante um minuto antes de exibir este erro: <br>Falhou em enumerar o diretório https://... O nome remoto não podia ser resolvido. `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões em: `C:\Windows\System32\drivers\etc\hosts` .|
|O comando AzCopy parece parar de responder durante um minuto antes de exibir este erro: <br>Localização da fonte de análise de erro. A ligação subjacente foi encerrada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS.|Importe o certificado TLS/SSL para o seu dispositivo na loja de certificados do sistema. Para mais informações, aceda ao [Download do certificado.](data-box-deploy-copy-data-via-rest.md#download-certificate)|


## <a name="errors-seen-in-azcopy-for-linux"></a>Erros vistos em AzCopy para Linux

Esta secção detalha alguns dos problemas enfrentados ao utilizar o AzCopy para Linux com armazenamento de Bolha de Caixa de Dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O comando AzCopy parece parar de responder durante 20 minutos antes de exibir este erro: <br>Localização da fonte de análise de erro `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` . Nenhum dispositivo ou endereço deste tipo|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões em: `/etc/hosts` .|
|O comando AzCopy parece parar de responder durante 20 minutos antes de exibir este erro: <br>Localização da fonte de análise de erro... A ligação SSL não pôde ser estabelecida.|Importe o certificado TLS/SSL para o seu dispositivo na loja de certificados do sistema. Para mais informações, aceda ao [Download do certificado.](data-box-deploy-copy-data-via-rest.md#download-certificate)|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Erros vistos na biblioteca de armazenamento Azure para Python

Esta secção detalha alguns dos principais problemas enfrentados durante a implementação do Disco caixa de dados quando se utiliza um cliente Linux para cópia de dados.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|O valor de um dos cabeçalhos HTTP não está no formato correto. |A versão instalada da Microsoft Azure Storage Library for Python não é suportada pela Data Box. Consulte os requisitos de armazenamento da Caixa de Dados Azure para versões suportadas.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...|Antes de executar python, desafie a variável ambiente REQUESTS_CA_BUNDLE para o caminho do ficheiro de certificado TLS codificado base64 (ver como [descarregar o certificado).](data-box-deploy-copy-data-via-rest.md#download-certificate) <br>Por exemplo:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>Em alternativa, adicione o certificado à loja de certificados do sistema e, em seguida, desacorda esta variável ambiental no caminho dessa loja. <br> Por exemplo, no Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Erros comuns

Estes erros não são específicos de qualquer aplicação.

|Mensagem de erro  |Ação recomendada |
|---------|---------|
|A ligação está fora. |Inscreva-se no dispositivo Data Box e verifique se está desbloqueado. Sempre que o dispositivo reinicia, fica bloqueado até alguém entrar.|

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os requisitos do [sistema de armazenamento de caixa de dados.](data-box-system-requirements-rest.md)

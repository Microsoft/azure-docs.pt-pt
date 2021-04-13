---
title: Utilize o portal Azure para resolver problemas no Azure Stack Edge Pro com GPU| Microsoft Docs
description: Descreve como resolver problemas com os problemas de GPU do Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 87e75d771c2cc269eaae81c2433f445eb65a17a9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314155"
---
# <a name="troubleshoot-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Problemas de resolução de problemas no seu dispositivo GPU Azure Stack Edge Pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Este artigo descreve como resolver problemas no seu dispositivo GPU Azure Stack Edge Pro. 


## <a name="run-diagnostics"></a>Executar diagnósticos

Para diagnosticar e resolver quaisquer erros do dispositivo, pode executar os testes de diagnóstico. Efetue os seguintes passos na IU da Web local no dispositivo para executar testes de diagnóstico.

1. Na IU da Web local, aceda a **Resolução de problemas > Testes de diagnóstico**. Selecione o teste que pretende executar e selecione **Teste de execução**. O teste diagnostica quaisquer problemas possíveis com a sua rede, dispositivo, procuração web, tempo ou configurações de nuvem. Será notificado de que o dispositivo está a executar testes.

    ![Selecione testes ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)
 
2. Depois de os testes estarem concluídos, os resultados são apresentados. 

    ![Ver resultados dos testes](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Se um teste falhar, é apresentado um URL para a ação recomendada. Selecione o URL para visualizar a ação recomendada.
 
    ![Avisos de revisão para testes falhados](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Recolher pacote de Suporte

Um pacote de registos é composto por todos os registos relevantes que possam ajudar o Suporte da Microsoft a resolver quaisquer problemas do dispositivo. Pode gerar um pacote de registos através da IU da Web local.

Efetue os seguintes passos para recolher um pacote de Suporte. 

1. Na IU da Web local, aceda a **Resolução de problemas > Suporte**. **Selecione Criar pacote de suporte**. O sistema começa a recolher o pacote de suporte. A recolha do pacote pode demorar vários minutos.

    ![Selecione adicionar utilizador](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Depois de criar o pacote Support, selecione **o pacote de Suporte para Download**. Um pacote zipado é transferido no caminho que escolher. Pode deszipar o pacote e ver os ficheiros de registo do sistema.

    ![Selecione adicionar utilizador 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Recolha registos de segurança avançados

Os registos de segurança avançados podem ser registos de intrusão de software ou hardware para o seu dispositivo Azure Stack Edge Pro.

### <a name="software-intrusion-logs"></a>Registos de intrusão de software

A intrusão do software ou os registos de firewall predefinidos são recolhidos para o tráfego de entrada e saída. 

- Quando o dispositivo é visualizado na fábrica, o registo de firewall predefinido está ativado. Estes registos são agregados no pacote de suporte por padrão quando cria um pacote de suporte através da UI local ou através da interface Windows PowerShell do dispositivo.

- Se apenas forem necessários registos de firewall no pacote de suporte para rever qualquer intrusão de software (NW) no dispositivo, utilize `-Include FirewallLog` a opção ao criar o pacote de suporte. 

- Se não for fornecida nenhuma opção específica de inclusão, o registo de firewall é incluído como padrão no pacote de suporte.

- No pacote de suporte, o registo de firewall é o `pfirewall.log` e fica na pasta raiz. Aqui está um exemplo do registo de intrusão de software para o dispositivo Azure Stack Edge Pro. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Troncos de intrusão de hardware

Para detetar qualquer intrusão de hardware no dispositivo, atualmente todos os eventos do chassis, como abertura ou fecho do chassis, estão registados. 

- O registo de eventos do sistema do dispositivo é lido utilizando o `racadm` cmdlet. Estes eventos são então filtrados para evento relacionado com chassis em um `HWIntrusion.txt` arquivo.

- Para obter apenas o login de intrusão de hardware no pacote de suporte, use a `-Include HWSelLog` opção quando criar o pacote de suporte. 

- Se não for fornecida nenhuma opção específica de inclusão, o registo de intrusão de hardware é incluído como padrão no pacote de suporte.

- No pacote de suporte, o registo de intrusão de hardware é o `HWIntrusion.txt` e fica na pasta raiz. Aqui está um exemplo do registo de intrusão de hardware para o dispositivo Azure Stack Edge Pro. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="use-logs-to-troubleshoot"></a>Utilizar registos para resolução de problemas

Quaisquer erros ocorridos durante os processos de carregamento e atualização são incluídos nos ficheiros de erro correspondente.

1. Para ver os ficheiros de erro, vá à sua parte e selecione a partilha para ver o conteúdo. 


2. Selecione a _pasta Microsoft Data Box Edge_. Esta pasta tem duas subpastas:

    - A pasta de carregamentos, que inclui os ficheiros de registos com erros de carregamento.
    - A pasta de atualizações para os erros durante a atualização.

    Eis um ficheiro de registo de exemplo para atualização.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Quando vir um erro neste ficheiro (realçado no exemplo), preste atenção ao código de erro, neste caso é 16001. Procure a descrição deste código de erro em relação à seguinte referência de erro.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="use-error-lists-to-troubleshoot"></a>Utilize listas de erros para resolução de problemas

As listas de erros são compiladas a partir de cenários identificados e podem ser usadas para autodiagnóstico e resolução de problemas. 

## <a name="azure-resource-manager"></a>Azure Resource Manager

Aqui estão os erros que podem aparecer durante a configuração do Azure Resource Manager para aceder ao seu dispositivo. 

| **Emissão / Erros** |  **Resolução** | 
|------------|-----------------|
|Problemas gerais|<li>[Verifique se o dispositivo Edge está configurado corretamente](#verify-the-device-is-configured-properly).<li> [Verifique se o cliente está configurado corretamente](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: Ocorreu um erro ao enviar o pedido.<br>Na linha:1 char:1<br>+ Add-AzureRmEnvironment -Nome Az3 -ARMEndpoint " https://management.dbe ...|Este erro significa que o seu dispositivo Azure Stack Edge Pro não está acessível ou configurado corretamente. Verifique se o dispositivo Edge e o cliente estão configurados corretamente. Para orientação, consulte as **questões gerais** nesta tabela.|
|Erro devolvido do serviço. Consulte o InnerException para obter mais detalhes: A ligação subjacente foi fechada: Não foi possível estabelecer uma relação de confiança para o canal seguro SSL/TLS. |   Este erro deve-se, provavelmente, a uma ou mais etapas do certificado incorretamente realizadas. Pode encontrar orientação [aqui.](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates) |
|Operação devolveu um código de estado inválido 'ServiceUnavailable' <br> O código de estado de resposta não indica sucesso: 503 (Serviço Indisponível). | Este erro pode ser o resultado de qualquer uma destas condições.<li>ArmStsPool está em estado de paragem.</li><li>Qualquer um dos websites de serviços Azure Resource Manager/Security token estão em baixo.</li><li>O recurso de cluster Azure Resource Manager está em baixo.</li><br><strong>Nota:</strong> Reiniciar o aparelho pode corrigir o problema, mas deve recolher o pacote de suporte para que possa desordiá-lo ainda mais.|
|AADSTS50126: Nome de utilizador ou senha inválido.<br>ID de traço: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>ID de correlação: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Horário: 2019-11-15 09:21:57Z: O servidor remoto devolveu um erro: (400) Mau Pedido.<br>Na linha:1 char:1 |Este erro pode ser o resultado de qualquer uma destas condições.<li>Para um nome de utilizador e senha inválidos, valide que o cliente alterou a palavra-passe do portal Azure seguindo os passos [aqui](/azure/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password) e, em seguida, utilizando a senha correta.<li>Para um ID inquilino inválido, o ID do inquilino é um GUID fixo e deve ser definido para `c0257de7-538f-415c-993a-1b87a031879d`</li>|
|connect-AzureRmAccount: AADSTS90056: O recurso está desativado ou não existe. Verifique o código da sua aplicação para garantir que especificou o URL de recurso exato para o recurso a que está a tentar aceder.<br>ID de traço: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>ID de correlação: 75c8ef5a-830e-48b5-b039-595a96488ff9 Timestamp: 2019-11-18 07:00:51Z: O servidor remoto devolveu um erro: (400) Bad |Os pontos finais de recursos utilizados no `Add-AzureRmEnvironment` comando estão incorretos.|
|Incapaz de obter pontos finais da nuvem.<br>Certifique-se de que tem ligação à rede. Detalhe de erro: HTTPSConnectionPool (host='management.dbg-of4k6suvm.microsoftdatabox.com', porta=30005): Retries max ultrapassados com url: /metadados/pontos finais?api-versão=2015-01-01 (Causado por SSLError("mau aperto de mão: Erro([rotinas SSL', 'tls_process_server_certificate', 'certificação verificar falhou')) |Este erro aparece principalmente num ambiente Mac/Linux, e deve-se às seguintes questões:<li>Um certificado de formato PEM não foi adicionado à loja de certificados python.</li> |

### <a name="verify-the-device-is-configured-properly"></a>Verifique se o dispositivo está configurado corretamente

1. A partir da UI local, verifique se a rede do dispositivo está configurada corretamente.

2. Verifique se os certificados são atualizados para todos os pontos finais mencionados [aqui.](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)

3. Obtenha a gestão do Gestor de Recursos Azure e início de sessão na página **do Dispositivo** na UI local.

4. Verifique se o dispositivo está ativado e registado em Azure.


### <a name="verify-the-client-is-configured-properly"></a>Verifique se o cliente está configurado corretamente

1. Valide que a versão correta do PowerShell está instalada como mencionado [aqui.](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)

2. Valide que os módulos PowerShell corretos estão instalados como mencionado [aqui.](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)

3. Valide que o Azure Resource Manager e os pontos finais de login são alcançáveis. Pode tentar marcar os pontos finais. Por exemplo:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Se não forem alcançáveis, adicione entradas de ficheiros DNS /host como mencionado [aqui](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. Validar que os certificados de cliente são instalados como mencionado [aqui.](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)

5. Se o cliente estiver a utilizar o PowerShell, deverá ativar a preferência de depurar para ver mensagens detalhadas executando este comando PowerShell. 

    `$debugpreference = "continue"`

## <a name="blob-storage-on-device"></a>Armazenamento de bolhas no dispositivo 

Aqui estão os erros relacionados com o armazenamento de bolhas no dispositivo Azure Stack Edge Pro/ Data Box Gateway.

| **Emissão / Erros** |  **Resolução** | 
|--------------------|-----------------|
|Incapaz de recuperar recursos infantis. O valor de um dos cabeçalhos HTTP não está no formato correto.| No menu **Editar,** selecione **ApIs target Azure Stack**. Em seguida, reinicie o Explorador de Armazenamento Azure.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões neste caminho: no Windows ou no `C:\Windows\System32\drivers\etc\hosts` `/etc/hosts` Linux.|
|Incapaz de recuperar recursos infantis.<br> Detalhes: certificado auto-assinado |Importe o certificado SSL para o seu dispositivo no Azure Storage Explorer: <ol><li>Faça o download do certificado do portal Azure. Para mais informações, consulte [Descarregar o certificado.](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)</li><li>No menu **Editar,** selecione Certificados SSL e, em seguida, selecione **Certificados de Importação**.</li></ol>|
|O comando AzCopy parece parar de responder durante um minuto antes de exibir este erro:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões em: `C:\Windows\System32\drivers\etc\hosts` .|
|O comando AzCopy parece parar de responder durante um minuto antes de exibir este erro:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importe o certificado SSL para o seu dispositivo na loja de certificados do sistema. Para mais informações, consulte [Descarregar o certificado.](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)|
|O comando AzCopy parece parar de responder durante 20 minutos antes de exibir este erro:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões em: `/etc/hosts` .|
|O comando AzCopy parece parar de responder durante 20 minutos antes de exibir este erro:<br>`Error parsing source location… The SSL connection could not be established`. |Importe o certificado SSL para o seu dispositivo na loja de certificados do sistema. Para mais informações, consulte [Descarregar o certificado.](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)|
|O comando AzCopy parece parar de responder durante 20 minutos antes de exibir este erro:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Verifique se o nome do ponto final `<accountname>.blob.<serialnumber>.microsoftdatabox.com` é adicionado ao ficheiro dos anfitriões em: `/etc/hosts` .|
|O comando AzCopy parece parar de responder durante 20 minutos antes de exibir este erro: `Error parsing source location… The SSL connection could not be established` .|Importe o certificado SSL para o seu dispositivo na loja de certificados do sistema. Para mais informações, consulte [Descarregar o certificado.](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate)|
|O valor de um dos cabeçalhos HTTP não está no formato correto.|A versão instalada da Microsoft Azure Storage Library for Python não é suportada pela Data Box. Consulte os requisitos de armazenamento da Caixa de Dados Azure para versões suportadas.|
|… [SSL: CERTIFICATE_VERIFY_FAILED] ...| Antes de executar python, desafie a variável ambiente REQUESTS_CA_BUNDLE para o caminho do certificado SSL codificado base64 (ver como [descarregar o certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate). Por exemplo:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>Em alternativa, adicione o certificado à loja de certificados do sistema e, em seguida, coloque esta variável ambiental no caminho dessa loja. Por exemplo, no Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`.|
|A ligação está fora.|Inscreva-se no Azure Stack Edge Pro e verifique se está desbloqueado. Sempre que o dispositivo reinicia, fica trancado até alguém entrar.|

## <a name="troubleshoot-iot-edge-errors"></a>Erros de IoT Edge de resolução de problemas

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como [resolver problemas de ativação do dispositivo](azure-stack-edge-gpu-troubleshoot-activation.md).

---
title: 'Quickstart: Extrair dados de fatura usando Python - Reconhecimento de formulários'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você usará o API de Reconhecimento de Formulários REST com Python para extrair dados de faturas
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/12/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 2ed8bdd167814ea21fced89042bdcf80fd3a73df
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602653"
---
# <a name="quickstart-extract-invoice-data-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Extrair dados da fatura utilizando o Formulário Reconhecedor REST API com Python

Neste arranque rápido, você usará o API API do Reconhecimento de FormulárioS Azure com Python para extrair e identificar informações relevantes em faturas.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você deve ter:
- [Python](https://www.python.org/downloads/) instalado (se quiser executar a amostra localmente).
- Um documento de fatura. Pode utilizar a fatura da [amostra](../media/sample-invoice.jpg) para este arranque rápido.

> [!NOTE]
> Este quickstart usa um ficheiro local. Para utilizar um documento de fatura acedido por URL, consulte a [documentação de referência](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-an-invoice"></a>Analisar uma fatura

Para começar a analisar uma fatura, ligue para a **[API de fatura de análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** utilizando o script Python abaixo. Antes de executar o script, faça estas alterações:

1. `<Endpoint>`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `<subscription key>`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. `<path to your invoice>`Substitua-o pelo caminho local onde tenha uma fatura de amostra guardada.

    ```python
        ########### Python Form Recognizer Async Invoice #############
    
        import json
        import time
        from requests import get, post
        
        # Endpoint URL
        endpoint = r"<Endpoint>"
        apim_key = "<subscription key>"
        post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
        source = r"<path to your invoice>"
        
        headers = {
            # Request headers
            'Content-Type': '<file type>',
            'Ocp-Apim-Subscription-Key': apim_key,
        }
        
        params = {
            "includeTextDetails": True
            "locale": "en-US"
        }
        
        with open(source, "rb") as f:
            data_bytes = f.read()
        
        try:
            resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
            if resp.status_code != 202:
                print("POST analyze failed:\n%s" % resp.text)
                quit()
            print("POST analyze succeeded:\n%s" % resp.headers)
            get_url = resp.headers["operation-location"]
        except Exception as e:
            print("POST analyze failed:\n%s" % str(e))
            quit()
    ```

1. Guarde o código num ficheiro com uma extensão .py. Por exemplo, *form-recognizer-invoice.py.*
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-invoice.py`.

Receberá uma `202 (Success)` resposta que inclui um **cabeçalho operação-localização,** que o script irá imprimir na consola. Este cabeçalho contém um ID de funcionamento que pode utilizar para consultar o estado da operação assíncronea e obter os resultados. No seguinte valor de exemplo, a cadeia seguinte `operations/` é o ID de funcionamento.

## <a name="get-the-invoice-results"></a>Obtenha os resultados da fatura

Depois de ter chamado a **API de Fatura de Análise,** ligue para a API **[do Resultado da Fatura de Análise](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** para obter o estado da operação e os dados extraídos. Adicione o seguinte código na parte inferior do seu script Python. Isto utiliza o valor de ID de operação numa nova chamada da API. Este script chama a API em intervalos regulares até que os resultados estejam disponíveis. Recomendamos um intervalo de um segundo ou mais.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Invoice results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Invoice Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Invoice Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Guarde o guião.
1. Volte a usar o `python` comando para executar a amostra. Por exemplo, `python form-recognizer-invoice.py`.

### <a name="examine-the-response"></a>Examinar a resposta

O script imprimirá respostas à consola até que a operação **'Fatura de Análise'** esteja concluída. Em seguida, imprimirá os dados de texto extraídos no formato JSON. O `"readResults"` campo contém todas as linhas de texto que foram extraídas da fatura, `"pageResults"` incluindo as tabelas e marcas de seleções extraídas da fatura e o `"documentResults"` campo contém informações de chave/valor para as partes mais relevantes da fatura.

Consulte o seguinte documento de fatura e a respetiva saída JSON:

* [Fatura da amostra](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)
* [Amostra fatura saída JSON](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice-output.json)


### <a name="sample-python-script-to-extract-invoice-or-a-batch-of-invoices-into-a-csv-file"></a>Prove o script Python para extrair fatura ou um lote de faturas num ficheiro CSV

Esta amostra de script python mostra-lhe como começar a usar a API de fatura. Pode funcionar com uma única fatura como parâmetro ou pasta e irá desautor o ficheiro JSON ".invoice.jsligado" e um ficheiro CSV _invoiceResutls.csv_ com os resultados dos valores extraídos. Ao correr numa pasta, irá analisar todos os ficheiros "pdf", "jpg", "jpeg","png","bmp", "tif", "tiff" e executá-los com a API. 
 
```python
########### Python Form Recognizer Async Invoice #############

import json
import time
import os
import ntpath
import sys
from requests import get, post
import csv


def analyzeInvoice(filename):
    invoiceResultsFilename = filename + ".invoice.json"

    # do not run analyze if .invoice.json file is present on disk
    if os.path.isfile(invoiceResultsFilename):
        with open(invoiceResultsFilename) as json_file:
            return json.load(json_file)

    # Endpoint URL
    #endpoint = r"<Endpoint>"
    #apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
    headers = {
        # Request headers
        'Content-Type': 'application/octet-stream',
        'Ocp-Apim-Subscription-Key': apim_key,
    }

    params = {
        "includeTextDetails": True
    }

    with open(filename, "rb") as f:
        data_bytes = f.read()

    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            return None
        print("POST analyze succeeded: %s" % resp.headers["operation-location"])
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        return None

    n_tries = 50
    n_try = 0
    wait_sec = 6

    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
            resp_json = json.loads(resp.text)
            if resp.status_code != 200:
                print("GET Invoice results failed:\n%s" % resp_json)
                return None
            status = resp_json["status"]
            if status == "succeeded":
                print("Invoice analysis succeeded.")
                with open(invoiceResultsFilename, 'w') as outfile:
                    json.dump(resp_json, outfile, indent=4)
                return resp_json
            if status == "failed":
                print("Analysis failed:\n%s" % resp_json)
                return None
            # Analysis still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1     
        except Exception as e:
            msg = "GET analyze results failed:\n%s" % str(e)
            print(msg)
            return None

    return resp_json

def parseInvoiceResults(resp_json):
    docResults = resp_json["analyzeResult"]["documentResults"]
    invoiceResult = {}
    for docResult in docResults:
        for fieldName, fieldValue in sorted(docResult["fields"].items()):
            valueFields = list(filter(lambda item: ("value" in item[0]) and ("valueString" not in item[0]), fieldValue.items()))
            invoiceResult[fieldName] = fieldValue["text"]            
            if len(valueFields) == 1:
                print("{0:26} : {1:50}      NORMALIZED VALUE: {2}".format(fieldName , fieldValue["text"], valueFields[0][1]))
                invoiceResult[fieldName + "_normalized"] = valueFields[0][1]
            else:
                print("{0:26} : {1}".format(fieldName , fieldValue["text"]))
    print("")
    return invoiceResult

def main(argv):
    if (len(argv)  != 2):
        print("ERROR: Please provide invoice filename or root directory with invoice PDFs/images as an argument to the python script")
        return

    # list of invoice to analyze
    invoiceFiles = []
    csvPostfix = '-invoiceResults.csv'
    if os.path.isfile(argv[1]):
        # Single invoice
        invoiceFiles.append(argv[1])
        csvFileName = argv[1] + csvPostfix
    else:
        # Folder of invoices
        supportedExt = ['.pdf', '.jpg','.jpeg','.tif','.tiff','.png','.bmp']
        invoiceDirectory = argv[1]
        csvFileName = os.path.join(invoiceDirectory, os.path.basename(os.path.abspath(invoiceDirectory)) + csvPostfix)
        for root, directories, filenames in os.walk(invoiceDirectory):
            for invoiceFilename in filenames:
                ext = os.path.splitext(invoiceFilename)[-1].lower()
                if ext in supportedExt:
                    fullname = os.path.join(root, invoiceFilename)
                    invoiceFiles.append(fullname)
                    
    with open(csvFileName, mode='w', newline='\n', encoding='utf-8') as csv_file:
        fieldnames = ['Filename',
                      'FullFilename','InvoiceTotal','InvoiceTotal_normalized','AmountDue','AmountDue_normalized','SubTotal','SubTotal_normalized','TotalTax','TotalTax_normalized','CustomerName','VendorName',
                      'InvoiceId','CustomerId','PurchaseOrder','InvoiceDate','InvoiceDate_normalized','DueDate','DueDate_normalized',
                      'VendorAddress','VendorAddressRecipient','BillingAddress','BillingAddressRecipient','ShippingAddress','ShippingAddressRecipient','CustomerAddress','CustomerAddressRecipient','ServiceAddress','ServiceAddressRecipient','RemittanceAddress','RemittanceAddressRecipient', 'ServiceStartDate','ServiceStartDate_normalized','ServiceEndDate','ServiceEndDate_normalized','PreviousUnpaidBalance','PreviousUnpaidBalance_normalized']
        writer = csv.DictWriter(csv_file, fieldnames=fieldnames)
        writer.writeheader()
        counter = 0
        for invoiceFullFilename in invoiceFiles:
            counter = counter + 1
            invoiceFilename = ntpath.basename(invoiceFullFilename)
            print("----- Processing {0}/{1} : {2} -----".format(counter, len(invoiceFiles),invoiceFullFilename))
            
            resp_json = analyzeInvoice(invoiceFullFilename)

            if (resp_json is not None):
                invoiceResults = parseInvoiceResults(resp_json)
                invoiceResults["FullFilename"] = invoiceFullFilename
                invoiceResults["Filename"] = invoiceFilename
                writer.writerow(invoiceResults)
                    
if __name__ == '__main__':
    main(sys.argv)
```

1. Guarde o código num ficheiro com uma extensão .py. Por exemplo, *form-recognizer-invoice-to-csv.py.*
1. `<Endpoint>`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `<subscription key>`Substitua-a pela chave de subscrição que copiou do passo anterior.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-invoice.py {file name or folder name}` o script Python pode ser executado com uma única fatura ou uma pasta como parâmetro e irá desautor o ficheiro JSON ".invoice.jsligado" e os valores extraídos das faturas num ficheiro CSV "-invoiceResults.csv" com os resultados. Ao correr numa pasta, irá analisar todos os ficheiros "pdf", "jpg", "jpeg","png","bmp", "tif", "tiff" e executá-los com a API.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, utilizou o Formulário Reconhecedor REST API com Python para extrair o conteúdo das faturas. Em seguida, consulte a documentação de referência para explorar mais aprofundadamente a API do Reconhecimento de Formulários.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)

   

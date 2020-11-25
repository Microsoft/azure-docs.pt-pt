---
title: Web App Tutorial - Teste de ligação à AZure API para FHIR
description: Estes tutoriais passam por um exemplo de implementação de uma simples aplicação web. Esta secção do tutorial passa por testes de ligação ao servidor FHIR com o Carteiro
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023317"
---
# <a name="testing-the-fhir-api"></a>Testar a FHIR API
Nas duas etapas anteriores, implementou a AZure API para FHIR e registou a sua candidatura ao cliente. Está agora pronto para testar que a sua API Azure para FHIR está configurada com a sua aplicação ao cliente. 

## <a name="retrieve-capability-statement"></a>Declaração de capacidade de recuperação
Primeiro, obteremos a declaração de capacidade para a sua Azure API para fHIR. 
1. Carteiro Aberto
1. Recupere a declaração de capacidade fazendo https:// \<FHIR-SERVER-NAME> GET .azurehealthcareapis.com/metadata. Na imagem abaixo o nome do servidor FHIR é **fhirserver**.

![Declaração de Capacidade](media/tutorial-web-app/postman-capability-statement.png)

Em seguida, tentaremos recuperar um paciente. Para recuperar um paciente, introduza https:// \<FHIR-SERVER-NAME> GET .azurehealthcareapis.com/Patient. Receberá um erro não autorizado 401. Este erro deve-se ao facto de não ter provado que deve ter acesso aos dados dos pacientes.

## <a name="get-patient-from-fhir-server"></a>Obtenha paciente do servidor FHIR
![Paciente falhado](media/tutorial-web-app/postman-patient-authorization-failed.png)

Para ter acesso, precisa de um token de acesso.
1. No Carteiro, selecione **Autorização** e desacione o Tipo **para OAuth2.0**
1. **Selecione Obter Novo Token de Acesso**
1. Preencha os campos e selecione **Request Token**. Abaixo pode ver os valores de cada campo para este tutorial.

|Campo                |Valor                                                               |
|---------------------|--------------------------------------------------------------------|
|Nome do Token           |Um nome para o seu símbolo                                               |
|Tipo de Concessão           |Código de Autorização                                                  |
|URL de chamada de retorno         |https://www.getpostman.com/oauth2/callback                          |
|URL Auth             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/?resource=https:// \<FHIR-SERVER-NAME> .azurehealthcareapis.com|
|URL do Token de Acesso     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID>/oauth2/token|
|ID de Cliente            |A iD do cliente que copiou durante os passos anteriores             |
|Segredo do Cliente        |\<BLANK>                                                            |
|Âmbito                |\<BLANK>                                                            |
|Estado                |1234                                                                |
|Autenticação de Cliente|Enviar credenciais de cliente no corpo                                     |

4. Inscreva-se com as suas credenciais e **selecione Aceitar**
1. Desloque-se para baixo no resultado e selecione **Use Token**
1. Selecione **Enviar** novamente no topo e desta vez deverá obter um resultado ![ Paciente de Sucesso](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Colocar o paciente no servidor FHIR
Agora que tem acesso, pode criar um novo paciente. Aqui está uma amostra de um paciente simples que pode adicionar no seu servidor FHIR. Introduza o código abaixo na secção **Corpo** do Carteiro.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Este POST criará um novo paciente no seu servidor FHIR com o nome James Tiberious Kirk.
![Pós-Paciente](media/tutorial-web-app/postman-post-patient.png)

Se fizeres o PASSO GET acima para recuperares um paciente outra vez, verás o James Tiberious Kirk listado na saída.

## <a name="troubleshooting-access-issues"></a>Resolução de problemas de acesso
Se tiver problemas durante qualquer uma destas etapas, reveja os documentos que reunimos no Azure Ative Directory e na AZure API para a FHIR. 

* [AZure AD e Azure API para fHIR](azure-ad-hcapi.md) - Este documento descreve alguns dos princípios básicos do Azure Ative Directory e como interage com a API Azure para fHIR.
* [Validação de token de acesso](azure-ad-hcapi-token-validation.md) - Este guia de como fazer fornece detalhes mais específicos sobre a validação do token de acesso e as medidas a tomar para resolver problemas de acesso.

## <a name="next-steps"></a>Passos Seguintes
Agora que pode ligar-se com sucesso à sua aplicação de cliente, está pronto para escrever a sua aplicação web.

>[!div class="nextstepaction"]
>[Escrever uma aplicação web](tutorial-web-app-write-web-app.md)




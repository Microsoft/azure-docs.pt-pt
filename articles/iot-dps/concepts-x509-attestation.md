---
title: Serviço de Provisionamento de Dispositivos Azure IoT Hub - Atestado de certificado X.509
description: Descreve conceitos específicos para a utilização de atestado de certificado X.509 com serviço de provisionamento de dispositivos (DPS) e IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3e06c79b9cbd5643d119974a4ed8628ea1b1cd4f
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096764"
---
# <a name="x509-certificate-attestation"></a>Atestado de certificado X.509

Este artigo apresenta uma visão geral dos conceitos do Serviço de Provisionamento de Dispositivos (DPS) envolvidos no provisionamento de dispositivos que utilizam atestado de certificado X.509. Este artigo é relevante para todas as personalidades envolvidas na obtenção de um dispositivo pronto para implantação.

Os certificados X.509 podem ser armazenados num módulo de segurança de hardware HSM.

> [!TIP]
> Recomendamos vivamente o uso de um HSM com dispositivos para armazenar de forma segura segredos, como o certificado X.509, nos seus dispositivos em produção.


## <a name="x509-certificates"></a>Certificados X.509

A utilização de certificados X.509 como mecanismo de atestado é uma excelente forma de escalar a produção e simplificar o fornecimento de dispositivos. Os certificados X.509 são normalmente dispostos numa cadeia de certificados de confiança em que cada certificado da cadeia é assinado pela chave privada do certificado superior seguinte, e assim por diante, terminando num certificado de raiz auto-assinado. Este acordo estabelece uma cadeia de confiança delegada a partir do certificado de raiz gerado por uma autoridade de certificados de raiz fidedigna (CA) através de cada CA intermédia até ao certificado "folha" da entidade final instalado num dispositivo. Para saber mais, consulte [a autenticação do dispositivo utilizando certificados X.509 CA](/azure/iot-hub/iot-hub-x509ca-overview). 

Muitas vezes, a cadeia de certificados representa alguma hierarquia lógica ou física associada aos dispositivos. Por exemplo, um fabricante pode:
- emitir um certificado de CA raiz auto-assinado
- usar o certificado raiz para gerar um certificado de CA intermediário único para cada fábrica
- utilizar o certificado de cada fábrica para gerar um certificado de CA intermédio único para cada linha de produção na fábrica
- e, finalmente, utilizar o certificado da linha de produção, para gerar um certificado único de dispositivo (entidade final) para cada dispositivo fabricado na linha. 

Para saber mais, consulte [a compreensão conceptual dos certificados X.509 CA na indústria IoT.](/azure/iot-hub/iot-hub-x509ca-concept) 

### <a name="root-certificate"></a>Certificado de raiz

Um certificado de raiz é um certificado X.509 auto-assinado que representa uma autoridade de certificados (CA). É o terminus, ou âncora de confiança, da cadeia de certificados. Os certificados de raiz podem ser emitidos por uma organização ou adquiridos a uma autoridade de certificados de raiz. Para saber mais, [consulte os certificados DE S.X.509](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)CA . O certificado de raiz também pode ser referido como um certificado de CA raiz.

### <a name="intermediate-certificate"></a>Certificado intermédio

Um certificado intermédio é um certificado X.509, assinado pelo certificado raiz (ou por outro certificado intermédio com o certificado raiz na sua cadeia). O último certificado intermédio de uma cadeia é utilizado para assinar o certificado de folha. Um certificado intermédio também pode ser referido como um certificado intermédio de CA.

##### <a name="why-are-intermediate-certs-useful"></a>Por que os certificados intermédios são úteis?
Os certificados intermédios são utilizados de várias maneiras. Por exemplo, os certificados intermédios podem ser usados para agrupar dispositivos por linhas de produtos, clientes que compram dispositivos, divisões da empresa ou fábricas. 

Imagine que a Contoso é uma grande empresa com a sua própria Infraestrutura de Chaves Públicas (PKI) usando o certificado raiz chamado *ContosoRootCert*. Cada subsidiária da Contoso tem o seu próprio certificado intermédio assinado pela *ContosoRootCert.* Cada subsidiária utilizará então o seu certificado intermédio para assinar os seus certificados de folha para cada dispositivo. Neste cenário, Contoso pode utilizar um único caso DPS onde *ContosoRootCert* foi verificado com [prova de posse](./how-to-verify-certificates.md). Podem ter um grupo de inscrições para cada subsidiária. Desta forma, cada filial individual não terá de se preocupar com a verificação dos certificados.


### <a name="end-entity-leaf-certificate"></a>Certificado "folha" de entidade final

O certificado de folha, ou certificado de entidade final, identifica o titular do certificado. Possui o certificado de raiz na sua cadeia de certificados, bem como certificados zero ou mais intermédios. O certificado de folha não é utilizado para assinar quaisquer outros certificados. Identifica exclusivamente o dispositivo ao serviço de fornecimento e é por vezes referido como o certificado do dispositivo. Durante a autenticação, o dispositivo utiliza a chave privada associada a este certificado para responder a um desafio de posse do serviço.

Os certificados folha utilizados com uma [inscrição individual](./concepts-service.md#individual-enrollment) têm a obrigação de que o **Nome do Sujeito** deve ser definido para o ID de inscrição individual. Os certificados de folha utilizados com uma entrada [de grupo de inscrição](./concepts-service.md#enrollment-group) devem ter o **Nome de Sujeito** definido para o ID do dispositivo pretendido que será mostrado nos **Registos** de Registo para o dispositivo autenticado no grupo de inscrição.

Para saber mais, consulte [dispositivos autenticadores assinados com certificados X.509 CA](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates).

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>Controlo do acesso do dispositivo ao serviço de fornecimento com certificados X.509

O serviço de fornecimento expõe dois tipos de matrícula que pode utilizar para controlar o acesso ao dispositivo com o mecanismo de atestado X.509:  

- As entradas individuais de [inscrição](./concepts-service.md#individual-enrollment) são configuradas com o certificado do dispositivo associado a um dispositivo específico. Estas entradas controlam as inscrições para dispositivos específicos.
- As inscrições em grupo de [inscrição](./concepts-service.md#enrollment-group) estão associadas a um certificado de CA intermédio ou raiz específico. Estas inscrições controlam as inscrições para todos os dispositivos que possuam esse certificado intermédio ou raiz na sua cadeia de certificados. 

#### <a name="dps-device-chain-requirements"></a>Requisitos da cadeia de dispositivos DPS

Quando um dispositivo está a tentar [registar-se](how-to-verify-certificates.md)através de DPS utilizando um grupo de matrículas, o dispositivo deve enviar a cadeia de certificados do certificado de folha para um certificado verificado com prova de posse . Caso contrário, a autenticação falhará.

Por exemplo, se apenas o certificado raiz for verificado e um certificado intermédio for enviado para o grupo de inscrição, o dispositivo deve apresentar a cadeia de certificados do certificado de folha todo o caminho até o certificado de raiz verificado. Esta cadeia de certificados incluiria quaisquer certificados intermédios. A autenticação falhará se o DPS não conseguir atravessar a cadeia de certificados para um certificado verificado.

Por exemplo, considere uma empresa que utilize a seguinte corrente de dispositivo para um dispositivo.

![Cadeia de certificados de dispositivo de exemplo](./media/concepts-x509-attestation/example-device-cert-chain.png) 

Apenas o certificado de raiz é verificado, e o certificado *intermédio2* é carregado no grupo de inscrição.

![Raiz de exemplo verificada](./media/concepts-x509-attestation/example-root-verified.png) 

Se o dispositivo enviar apenas a seguinte corrente do dispositivo durante o fornecimento, a autenticação falhará. Porque o DPS não pode tentar a autenticação assumindo a validade do certificado *intermédio1*

![Cadeia de certificados de falha de exemplo](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

Se o dispositivo enviar toda a cadeia do dispositivo da seguinte forma durante o fornecimento, então o DPS pode tentar a autenticação do dispositivo.

![Cadeia de certificados de dispositivo de exemplo](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> Os certificados intermédios também podem ser verificados com [prova de posse](how-to-verify-certificates.md)..


#### <a name="dps-order-of-operations-with-certificates"></a>Ordem de operações de DPS com certificados
Quando um dispositivo se conecta ao serviço de fornecimento, o serviço prioriza entradas de inscrição mais específicas em vez de inscrições menos específicas. Ou seja, se existir uma inscrição individual para o dispositivo, o serviço de prestação de serviços aplica essa entrada. Se não houver inscrição individual para o dispositivo e um grupo de inscrição para o primeiro certificado intermédio na cadeia de certificados do dispositivo, o serviço aplica essa entrada, e assim por diante, até à raiz. O serviço aplica a primeira entrada aplicável que encontra, de modo a que:

- Se a primeira inscrição encontrada estiver ativada, o serviço fornece o dispositivo.
- Se a primeira inscrição encontrada for desativada, o serviço não forneça o dispositivo.  
- Se não for encontrada nenhuma inscrição para qualquer um dos certificados da cadeia de certificados do dispositivo, o serviço não fornece o dispositivo. 

Este mecanismo e a estrutura hierárquica das cadeias de certificados proporcionam uma poderosa flexibilidade na forma como pode controlar o acesso a dispositivos individuais, bem como a grupos de dispositivos. Por exemplo, imagine cinco dispositivos com as seguintes cadeias de certificados: 

- *Dispositivo 1*: certificado de > certificado de > certificado A -> certificado 1
- *Dispositivo 2*: certificado de > certificado A -> certificado 2
- *Dispositivo 3*: certificado de > certificado de > certificado A -> certificado 3
- *Dispositivo 4*: certificado de > certificado B -> certificado 4
- *Dispositivo 5*: certificado de raiz -certificado > B -> certificado do dispositivo 5

Inicialmente, pode criar uma única inscrição em grupo ativada para o certificado raiz para permitir o acesso aos cinco dispositivos. Se o certificado B ficar mais tarde comprometido, pode criar uma entrada de grupo de inscrição desativada para o certificado B para impedir a inscrição do *Dispositivo 4* e *do Dispositivo 5.* Se ainda mais tarde *o Dispositivo 3* ficar comprometido, pode criar uma inscrição individual desativada para o seu certificado. Isto revoga o acesso ao *Dispositivo 3*, mas ainda permite que *o Dispositivo 1* e *o Dispositivo 2* se inscrevam.
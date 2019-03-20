---
title: Referência de serviço de cópia de segurança de infra-estrutura de pilha do Azure | Documentos da Microsoft
description: Este artigo contém o material de referência para o serviço de cópia de segurança de infraestrutura do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: b35b069f05f117b227a2edbf5595f2682b7f6e86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997263"
---
# <a name="infrastructure-backup-service-reference"></a>Referência de serviço de cópia de segurança de infra-estrutura

## <a name="azure-backup-infrastructure"></a>Infraestrutura de cópia de segurança do Azure

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack é composta por vários serviços que compõem o portal, o Azure Resource Manager, e a experiência de gestão da infraestrutura. A experiência de gestão de tipo de aplicação do Azure Stack se concentra na redução da complexidade exposta para o operador da solução.

Cópia de segurança de infra-estrutura foi concebida para internalizar a complexidade de criar cópias de segurança e restaurar os dados para serviços de infraestrutura, garantir que os operadores pode focar a solução de gerenciamento e manutenção de um SLA para os utilizadores.

Exportar os dados de cópia de segurança para uma partilha externa é necessário para evitar a armazenar cópias de segurança no mesmo sistema. Exigir uma partilha externa oferece ao administrador a flexibilidade para determinar onde pretende armazenar os dados com base nas políticas de BC/DR de empresa existentes. 

### <a name="infrastructure-backup-components"></a>Componentes de cópia de segurança da infraestrutura

Cópia de segurança da infraestrutura inclui os seguintes componentes:

 - **Controlador de cópia de segurança de infra-estrutura**  
 O controlador de cópia de segurança da infraestrutura é instanciado com e reside em cada Cloud do Azure Stack.
 - **Fornecedor de recursos de cópia de segurança**  
 O fornecedor de recursos de Backup (cópia de segurança RP) é composta pelo utilizador interface e a aplicação programa interfaces (API) s expor a funcionalidade básica de cópia de segurança para a infraestrutura do Azure Stack.

#### <a name="infrastructure-backup-controller"></a>Controlador de cópia de segurança de infra-estrutura

O controlador de cópia de segurança de infra-estrutura é um recurso de infraestrutura do serviço de serviço será instanciado para uma Cloud do Azure Stack. Recursos de cópia de segurança são criados num nível e captura de serviço específico da região dados regionais do AD, AC, Azure Resource Manager, CRP, o SRP, o NRP, o Cofre de chaves, o RBAC. 

### <a name="backup-resource-provider"></a>Fornecedor de recursos de cópia de segurança

O fornecedor de recursos de cópia de segurança apresenta uma interface de utilizador no portal do Azure Stack para a configuração básica e lista de recursos de cópia de segurança. Operador pode efetuar as seguintes operações na interface do usuário:

 - Ativar cópia de segurança pela primeira vez, fornecendo a localização de armazenamento externo, credenciais e chave de encriptação
 - Criar a vista de concluir a cópia de segurança recursos e recursos de estado a ser criado
 - Modifique a localização de armazenamento em que o controlador de cópia de segurança coloca os dados de cópia de segurança
 - Modificar as credenciais que utiliza o controlador de cópia de segurança para aceder à localização de armazenamento externo
 - Modificar a chave de encriptação que utiliza o controlador de cópia de segurança para encriptar as cópias de segurança 


## <a name="backup-controller-requirements"></a>Requisitos do controlador de cópia de segurança

Esta secção descreve os requisitos importantes para a infraestrutura de cópia de segurança. Recomendamos que reveja as informações cuidadosamente antes de ativar cópia de segurança para a sua instância do Azure Stack e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos incluem:

  - **Requisitos de software** – descreve as localizações de armazenamento suportadas e a orientação de dimensionamento. 
  - **Requisitos de rede** – descreve os requisitos de rede para localizações de armazenamento diferentes.  

### <a name="software-requirements"></a>Requisitos de software

#### <a name="supported-storage-locations"></a>Localizações de armazenamento suportadas

| Localização do armazenamento                                                                 | Detalhes                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partilha de ficheiros SMB alojada num dispositivo de armazenamento dentro do ambiente de rede fidedigna | Partilha SMB no mesmo datacenter em que o Azure Stack é implementado ou num centro de dados diferente. Várias instâncias do Azure Stack, podem utilizar a mesma partilha de ficheiros. |
| Partilha de ficheiros SMB no Azure                                                          | Não são atualmente suportados.                                                                                                                                 |
| Armazenamento de BLOBs no Azure                                                            | Não são atualmente suportados.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versões suportadas do SMB

| SMB | Versão |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Dimensionamento de localização de armazenamento 

Controlador de cópia de segurança de infra-estrutura faz a cópia de segurança de dados a pedido. Recomenda-se fazer o backup finalmente duas vezes um dia e manter no máximo de sete dias de cópias de segurança. 

**1811 e muito mais**

| Dimensionamento do ambiente | Tamanho previsto de cópia de segurança | Quantidade total de espaço necessário |
|-------------------|--------------------------|--------------------------------|
| 16 de 4 nós        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Pré-1811**

| Dimensionamento do ambiente | Tamanho previsto de cópia de segurança | Quantidade total de espaço necessário |
|-------------------|--------------------------|--------------------------------|
| 4-16 nós, ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>Requisitos da rede

| Localização do armazenamento                                                                 | Detalhes                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partilha de ficheiros SMB alojada num dispositivo de armazenamento dentro do ambiente de rede fidedigna | É necessária a porta 445 se a instância do Azure Stack reside num ambiente com firewall. Controlador de cópia de segurança da infraestrutura será feita uma ligação ao servidor de ficheiros SMB através da porta 445. |
| Para utilizar o FQDN do servidor de ficheiros, o nome tem de ser resolvido a partir do PEP             |                                                                                                                                                                                         |

> [!Note]  
> Não existem portas de entrada tem de ser aberto.

### <a name="encryption-requirements"></a>Requisitos de encriptação

A partir de 1901, o serviço de cópia de segurança da infraestrutura irá utilizar um certificado com uma chave pública (. CER) para encriptar os dados de cópia de segurança e um certificado com a chave privada (. PFX) para desencriptar os dados de cópia de segurança durante a recuperação de nuvem.   
 - O certificado é utilizado para o transporte de chaves e não é utilizado para estabelecer uma comunicação segura autenticada. Por esse motivo, o certificado pode ser um certificado autoassinado. O Azure Stack não é necessário verificar a fidedignidade para este certificado de raiz ou para acesso externo à internet não é necessária.
 
O certificado autoassinado possui duas partes, uma com a chave pública e uma com a chave privada:
 - Encripte dados da cópia de segurança: Certificado com a chave pública (exportada para. Ficheiro CER) é utilizado para encriptar dados da cópia de segurança
 - Desencriptar os dados de cópia de segurança: Certificado com a chave privada (exportada para. Ficheiro PFX) é utilizado para desencriptar os dados de cópia de segurança

O certificado com a chave pública (. CER) não é gerido pelo rotação secreta interna. Para girar o certificado, terá de criar um novo certificado autoassinado e atualizar as definições de cópia de segurança com o novo ficheiro (. CER).  
 - Todas as cópias de segurança existentes permanecem encriptadas utilizando a chave pública anterior. Novas cópias de segurança irão utilizar a nova chave pública. 
 
O certificado utilizado durante a recuperação da cloud com a chave privada (. PFX) não é mantido pela pilha do Azure por motivos de segurança. Este ficheiro tem de ser fornecida explicitamente durante a recuperação de nuvem.  

**Efeitos modo de compatibilidade** a partir de 1901, o suporte de chave de encriptação foi preterido e será removido numa versão futura. Se atualizou a partir 1811 com cópia de segurança já ativada com uma chave de encriptação, Azure Stack vão continuar a utilizar a chave de encriptação. Com versões anteriores modo de compatibilidade será suportado, pelo menos, 3 de versão. Após esse tempo, será necessário um certificado. 
 * A atualizar a partir da chave de encriptação para o certificado é uma operação unidirecional.  
 * Todas as cópias de segurança existentes permanecerão encriptadas com a chave de encriptação. Novas cópias de segurança irão utilizar o certificado. 

## <a name="infrastructure-backup-limits"></a>Limites de cópia de segurança de infra-estrutura

Considere estes limites, como planejar, implantar e operar as instâncias do Microsoft Azure Stack. A tabela seguinte descreve estes limites.

### <a name="infrastructure-backup-limits"></a>Limites de cópia de segurança da infraestrutura

| Identificador de limite                                                 | Limite        | Comentários                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Tipo de cópia de segurança                                                      | Apenas completo    | Controlador de cópia de segurança de infra-estrutura só suporta cópias de segurança completas. Cópias de segurança incrementais não são suportadas.                                          |
| Cópias de segurança agendadas                                                | Agendada e manuais  | Controlador de cópia de segurança suporta cópias de segurança agendadas e sob demanda                                                                                 |
| Tarefas de cópia de segurança em simultâneo máximas                                   | 1            | Apenas uma tarefa de cópia de segurança Active Directory é suportada por instância do controlador de cópia de segurança.                                                                  |
| Configuração de comutador de rede                                     | Não está no âmbito | Administrador tem de criar cópias de segurança a configuração de comutador de rede com as ferramentas do OEM. Consulte a documentação para o Azure Stack, fornecido por cada fornecedor de OEM. |
| Anfitrião de ciclo de vida do hardware                                          | Não está no âmbito | Administrador tem de fazer backup de anfitrião de ciclo de vida do Hardware usando ferramentas de OEM. Consulte a documentação para o Azure Stack, fornecido por cada fornecedor de OEM.      |
| Número máximo de partilhas de ficheiros                                    | 1            | Apenas uma partilha de ficheiros pode ser utilizada para armazenar cópias de segurança                                                                                        |
| Cópia de segurança dos serviços de aplicações, função, SQL, dados de fornecedor de recursos do mysql | Não está no âmbito | Veja a orientação publicada para a implantação e gerenciamento de valor agregado RPs criado pela Microsoft.                                                  |
| Fornecedores de cópia de segurança de terceiros de recursos                              | Não está no âmbito | Veja a orientação publicada para a implantação e gerenciamento de valor agregado RPs criados por fornecedores de terceiros.                                          |

## <a name="next-steps"></a>Passos Seguintes

 - Para saber mais sobre o serviço de cópia de segurança da infraestrutura, consulte [cópia de segurança e recuperação de dados para o Azure Stack com o serviço de cópia de segurança da infraestrutura](azure-stack-backup-infrastructure-backup.md).

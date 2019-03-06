---
title: Descrição geral do Microsoft Azure Data Box Disk | Microsoft Docs em dados
description: Descreve o Azure Data Box Disk, uma solução cloud que permite transferir grandes quantidades de dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: c39931ab5d391020e0e7b5ad26ebfa41806fc4ba
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404676"
---
# <a name="what-is-azure-data-box-disk"></a>O que é o Azure Data Box Disk?

A solução Microsoft Azure Data Box Disk permite enviar terabytes de dados no local para o Azure de forma rápida, económica e fiável. A transferência de dados segura é acelerada pelo envio de 1 a 5 discos de estado sólido (SSDs). Estes discos encriptados de 8 TB são enviados para o seu datacenter através de uma operadora regional. 

Pode configurar, ligar e desbloquear rapidamente os discos através do serviço Data Box no portal do Azure. Copie os dados para discos e envie os discos novamente para o Azure. No datacenter do Azure, os seus dados são carregados automaticamente de unidades para a cloud através de uma ligação de carregamento de rede privada rápida.

## <a name="use-cases"></a>Casos de utilização

Utilize o Data Box Disk para transferir TBs de dados em cenários sem conectividade de rede limitada. O movimento de dados pode ser único, periódico ou uma transferência de dados em massa inicial seguida de transferências periódicas. 

- **Migração única** - Quando é movida uma grande quantidade de dados no local para o Azure. Por exemplo, mover dados de bandas offline para dados de arquivo no armazenamento esporádico do Azure.
- **Transferência incremental** - Quando é efetuada uma transferência em massa inicial com o Data Box Disk (seed) seguida de transferências incrementais através da rede. Por exemplo, são utilizados o Commvault e o Data Box Disk para mover as cópias de segurança para o Azure. Esta migração é seguida da cópia de dados incrementais através da rede para o armazenamento do Azure. 
- **Carregamentos periódicos** - Quando é gerada periodicamente uma grande quantidade de dados e tem de ser movida para o Azure. Por exemplo, na exploração de energia, em que o conteúdo de vídeo é gerado em plataformas petrolíferas e parques eólicos.

## <a name="the-workflow"></a>Fluxo de trabalho

Um fluxo típico inclui os seguintes passos:

1. **Encomenda** - Crie uma encomenda no portal do Azure, forneça as informações de envio e a conta de armazenamento do Azure de destino para os seus dados. Se os discos estiverem disponíveis, o Azure encripta-os, prepara-os e envia-os com um ID de controlo de envio.

2. **Receber** - Assim que os discos forem entregues, desembale-os e ligue-os ao computador que tem os dados a copiar. Desbloqueie os discos.
    
3. **Copiar dados** - Arraste e largue para copiar os dados para os discos.

4. **Devolver** - Prepare e envie os discos de volta para o datacenter do Azure.

5. **Carregar** - Os dados são copiados automaticamente dos discos para o Azure. Os discos são apagados em segurança de acordo com as diretrizes do National Institute of Standards and Technology (NIST).

Ao longo deste processo, será notificado por e-mail sobre todas as alterações de estado. Para obter mais informações sobre o fluxo detalhado, aceda a [Implementar Data Box Disks no portal do Azure](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Benefícios

O Data Box Disk foi concebido para mover grandes quantidades de dados para o Azure sem afetar a rede. A solução possui os benefícios seguintes:

- **Velocidade** - O Data Box Disk utiliza uma ligação USB 3.0 para mover até 35 TB de dados para o Azure em menos de uma semana.   

- **Fácil de utilizar** - O Data Box é uma solução de fácil utilização.

    - Os discos utilizam a conectividade USB quase sem tempo de configuração.
    - Os discos têm um fator de forma pequeno que facilita a utilização.
    - Os discos não têm requisitos de alimentação externa.
    - Os discos podem ser utilizados com servidores de datacenter, computadores de secretária e portáteis.
    - A solução fornece controlo ponto a ponto através do portal do Azure.    

- **Proteger** - O Data Box Disk tem proteções de segurança incorporadas para os discos, os dados e o serviço. 
    - Os discos são resistentes a adulterações e suportam atualizações seguras. 
    - Os dados nos discos são sempre protegidos com uma encriptação AES de 128 bits. 
    - Os discos só podem ser desbloqueados com uma chave fornecida no portal do Azure. 
    - O serviço está protegido pelos recursos de segurança do Azure. 
    - Assim que os dados forem carregados para o Azure, são apagados de acordo com as normas 800-88r1 do NIST.  
    
Para obter mais informações, aceda a [Proteção de dados e segurança do Azure Data Box Disk](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funcionalidades e especificações


| Especificações                                          | Descrição              |
|---------------------------------------------------------|--------------------------|
| Peso                                                  | < 1 kg por caixa. Até 5 discos na caixa de                |
| Dimensões                                              | Disco - SSD de 2,5" |            
| Cabos                                                  | 1 cabo USB 3.1 por disco|
| Capacidade de armazenamento por encomenda                              | 40 TB (~ 35 TB utilizáveis)|
| Capacidade de armazenamento do disco                                   | 8 TB (~ 7 TB utilizáveis)|
| Interface de dados                                          | USB   |
| Segurança                                                | Pré-encriptada com BitLocker e atualizações seguras <br> Discos protegidos por chave de acesso <br> Dados sempre encriptados  |
| Taxa de transferência de dados                                      | até 430 MBps, consoante o tamanho do ficheiro      |
|Gestão                                               | Portal do Azure |


## <a name="region-availability"></a>Disponibilidade de região

Atualmente o disco Data Box podem transferir dados para as regiões do Azure seguintes:


|Região do Azure  |Região do Azure  |
|---------|---------|
|EUA Centro-Oeste     |Canadá Central       |        
|E.U.A. Oeste 2     |Leste do Canadá         |     
|EUA Oeste     | Europa Ocidental        |      
|EUA Centro-Sul   |Europa do Norte     |         
|EUA Central     |Leste da Austrália|
|EUA Centro-Norte  |Sudeste da Austrália   |
|EUA Leste      |Austrália Central |
|E.U.A. Leste 2     |Austrália Central 2|
|Leste do Japão     |Coreia do Sul Central |


## <a name="pricing"></a>Preços

Para informações sobre preços, aceda a [página de preços](https://azure.microsoft.com/pricing/details/storage/databox/disk/).

## <a name="next-steps"></a>Passos Seguintes

- Reveja os [Requisitos do Data Box Disk](data-box-disk-system-requirements.md).
- Compreenda os [Limites do Data Box Disk](data-box-disk-limits.md).
- Implemente rapidamente o [Azure Data Box Disk](data-box-disk-quickstart-portal.md) no portal do Azure.

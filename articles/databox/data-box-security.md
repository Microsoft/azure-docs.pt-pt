---
title: Descrição geral da segurança do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve as funcionalidades de segurança do Azure Data Box no dispositivo, no serviço e nos dados que residem no Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: alkohli
ms.openlocfilehash: 00b6fab2aeebeeb0f39e07a0751e02cefd195b28
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337363"
---
# <a name="azure-data-box-security-and-data-protection"></a>Proteção de dados e segurança do Azure Data Box

O Data Box oferece uma solução segura para proteção de dados ao garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados. Este artigo descreve as funcionalidades de segurança do Azure Data Box que ajudam a proteger todos os componentes da solução Data Box e os respetivos dados armazenados. 

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Fluxo de dados através dos componentes

A solução Microsoft Azure Data Box é composta por quatro componentes principais que interagem entre si:

- **Serviço Azure Data Box alojado no Azure** – O serviço de gestão que utiliza para criar a encomenda dos dispositivos, configurar os dispositivos e, em seguida, controlar a encomenda até à conclusão.
- **Dispositivo Data Box** – O dispositivo de transferência que é enviado para si para importar os dados no local para o Azure. 
- **Clientes/anfitriões ligados ao dispositivo** – Os clientes na sua infraestrutura que ligam ao dispositivo Data Box e contêm dados que precisam de ser protegidos.
- **Armazenamento na cloud** – A localização na cloud do Azure onde os dados são armazenados. Normalmente, trata-se da conta de armazenamento ligada ao recurso do Azure Data Box que criou.

O diagrama seguinte indica o fluxo de dados através da solução Azure Data Box, das instalações para o Azure e as várias funcionalidades de segurança em vigor à medida que os dados fluem através da solução. Este fluxo destina-se a uma encomenda de importação para a sua Caixa de Dados.

![Garantia de importação de caixa de dados](media/data-box-security/data-box-security-import.png)

O diagrama a seguir é para a encomenda de exportação da sua Caixa de Dados.

![Garantia de exportação da Caixa de Dados](media/data-box-security/data-box-security-export.png)

À medida que os dados fluem através desta solução, os eventos são registados e os registos são gerados. Para mais informações, aceda a:

- [Rastreio e registo de eventos para as suas encomendas de importação da Caixa de Dados Azure.](data-box-logs.md)
- [Rastreio e registo de eventos para as suas encomendas de exportação da Azure Data Box](data-box-export-logs.md)

## <a name="security-features"></a>Funcionalidades de segurança

O Data Box oferece uma solução segura para proteção de dados ao garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados. As funcionalidades de segurança desta solução destinam-se ao disco e ao serviço associado para garantir a segurança dos dados armazenados nos mesmos. 

### <a name="data-box-device-protection"></a>Proteção do dispositivo do Data Box

O dispositivo do Data Box está protegido pelas seguintes funcionalidades:

- Uma caixa robusta para o dispositivo resistente a choques, transporte adverso e condições ambientais. 
- Deteção de adulteração de hardware e software que impede outras operações do dispositivo.
- Executa apenas o software específico do Data Box.
- Arranca num estado bloqueado.
- Controla o acesso do dispositivo através da palavra-passe de desbloqueio do dispositivo.
- Credenciais de acesso para copiar dados dentro e fora do dispositivo. Todos os acessos à página **de credenciais do Dispositivo** no portal Azure estão registados nos [registos](data-box-logs.md#query-activity-logs-during-setup)de atividade .

### <a name="data-box-data-protection"></a>Proteção de dados do Data Box

O fluxo de dados de entrada e saída do Data Box está protegido pelas seguintes funcionalidades:

- Encriptação AES de 256 bits para Dados inativos.
- Os protocolos encriptados podem ser utilizados para dados em trânsito. Recomendamos que utilize o SMB 3.0 com encriptação para proteger os dados quando os copiar dos seus servidores de dados.
- Eliminação segura de dados do dispositivo após a conclusão do carregamento de dados para o Azure. A eliminação de dados está de acordo com as diretrizes do [Apêndice A para discos rígidos ATA nas normas NIST 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). O evento de apagamento de dados é registado no histórico de [encomendas](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Proteção do serviço Data Box

O serviço Data Box está protegido pelas seguintes funcionalidades.

- O acesso ao serviço Data Box requer que a sua organização tenha uma subscrição do Azure que inclua o Data Box. A subscrição controla as funcionalidades a que pode aceder no portal do Azure.
- Uma vez que o serviço Data Box está alojado no Azure, é protegido pelas funcionalidades de segurança do Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- O acesso à ordem Caixa de Dados pode ser controlado através da utilização de funções Azure. Para obter mais informações, consulte [Configurar o controlo de acesso para a encomenda caixa de dados](data-box-logs.md#set-up-access-control-on-the-order)
- O serviço Data Box armazena a palavra-passe desbloqueada que serve para desbloquear o dispositivo no serviço.
- O serviço Data Box armazena os detalhes da encomenda e o estado no serviço. Estas informações são eliminadas quando a encomenda é eliminada.

## <a name="managing-personal-data"></a>Gerir dados pessoais

O Azure Data Box recolhe e apresenta informações pessoais nas seguintes instâncias-chave no serviço:

- **Definições de notificação** – Quando cria uma encomenda, pode configurar o endereço de e-mail dos utilizadores nas definições de notificação. Estas informações podem ser visualizadas pelo administrador. Estas informações são eliminadas pelo serviço quando a tarefa atinge o estado terminal ou ao eliminar a encomenda.

- **Detalhes da encomenda** – Depois de criada a encomenda, o endereço de envio, o e-mail e as informações de contacto dos utilizadores são armazenados no portal do Azure. As informações guardadas incluem:

  - Nome do contacto
  - Número de telefone
  - E-mail
  - Morada
  - City
  - Código postal
  - Estado
  - País/Província/Região
  - Número de conta da operadora
  - Número de controlo de envio

    Os detalhes da encomenda são eliminados pelo serviço Data Box quando a tarefa é concluída ou ao eliminar a encomenda.

- **Endereço de envio** – Depois de realizada a encomenda, o serviço Data Box oferece o endereço de envio a operadoras de terceiros, como UPS ou DHL. 

Para obter mais informações, reveja a política de privacidade da Microsoft no [Centro de Fidedignidade](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Referência das diretrizes de segurança

As seguintes diretrizes de segurança são implementadas no Data Box: 

|Diretriz   |Description   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Para proteção contra água e poeira         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Para resistir a condições de transporte adversas          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Para a atualização de firmware seguro         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Para proteção de dados         |
|Apêndice A, para Discos Rígidos ATA em [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Para a limpeza de dados         |

## <a name="next-steps"></a>Passos seguintes

- Reveja os [Requisitos do Data Box](data-box-system-requirements.md).
- Compreenda os [limites do Data Box](data-box-limits.md).
- Implemente rapidamente o [Azure Data Box](data-box-quickstart-portal.md) no portal do Azure.

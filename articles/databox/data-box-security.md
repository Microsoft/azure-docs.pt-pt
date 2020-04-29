---
title: Descrição geral da segurança do Microsoft Azure Data Box | Microsoft Docs em dados
description: Descreve as funcionalidades de segurança do Azure Data Box no dispositivo, no serviço e nos dados que residem no Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: alkohli
ms.openlocfilehash: 21b05631f1c225c9c4b1f7c65d18588900850b8e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77911729"
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

O diagrama seguinte indica o fluxo de dados através da solução Azure Data Box do local para o Azure.

![Segurança do Data Box](media/data-box-security/data-box-security-2.png)

À medida que os dados fluem através desta solução, os eventos são registados e os registos são gerados. Para mais informações, aceda ao Tracking e ao [registo de eventos para a sua Caixa](data-box-logs.md)de Dados Azure .

## <a name="security-features"></a>Funcionalidades de segurança

O Data Box oferece uma solução segura para proteção de dados ao garantir que apenas as entidades autorizadas podem ver, modificar ou eliminar os seus dados. As funcionalidades de segurança desta solução destinam-se ao disco e ao serviço associado para garantir a segurança dos dados armazenados nos mesmos. 

### <a name="data-box-device-protection"></a>Proteção do dispositivo do Data Box

O dispositivo do Data Box está protegido pelas seguintes funcionalidades:

- Uma caixa robusta para o dispositivo resistente a choques, transporte adverso e condições ambientais. 
- Selos invioláveis para garantir que nenhum dispositivo é adulterado durante o envio.
- Deteção de adulteração de hardware e software que impede outras operações do dispositivo.
- Executa apenas o software específico do Data Box.
- Arranca num estado bloqueado.
- Controla o acesso do dispositivo através da palavra-passe de desbloqueio do dispositivo.
- Credenciais de acesso para copiar dados dentro e fora do dispositivo. Todos os acessos à página de **credenciais do Dispositivo** no portal Azure estão registados nos [registos de atividade](data-box-logs.md#query-activity-logs-during-setup).

### <a name="data-box-data-protection"></a>Proteção de dados do Data Box

O fluxo de dados de entrada e saída do Data Box está protegido pelas seguintes funcionalidades:

- Encriptação AES de 256 bits para Dados inativos.
- Os protocolos encriptados podem ser utilizados para dados em trânsito.
- Eliminação segura de dados do dispositivo após a conclusão do carregamento de dados para o Azure. O apagamento de dados está de acordo com as diretrizes do Apêndice A para unidades de [disco rígido ATA nas normas NIST 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). O evento de apagamento de dados é registado no histórico da [ordem.](data-box-logs.md#download-order-history)

### <a name="data-box-service-protection"></a>Proteção do serviço Data Box

O serviço Data Box está protegido pelas seguintes funcionalidades.

- O acesso ao serviço Data Box requer que a sua organização tenha uma subscrição do Azure que inclua o Data Box. A subscrição controla as funcionalidades a que pode aceder no portal do Azure.
- Uma vez que o serviço Data Box está alojado no Azure, é protegido pelas funcionalidades de segurança do Azure. Para obter mais informações sobre as funcionalidades de segurança fornecidas pelo Microsoft Azure, aceda ao [Centro de Fidedignidade do Microsoft Azure](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- O acesso à ordem data box pode ser controlado através da utilização de funções de Controlo de Acesso (RBAC) baseados em funções. Para mais informações, consulte [Configurar o controlo de acesso para a ordem data box](data-box-logs.md#set-up-access-control-on-the-order)
- O serviço Data Box armazena a palavra-passe desbloqueada que serve para desbloquear o dispositivo no serviço.
- O serviço Data Box armazena os detalhes da encomenda e o estado no serviço. Estas informações são eliminadas quando a encomenda é eliminada.

## <a name="managing-personal-data"></a>Gerir dados pessoais

O Azure Data Box recolhe e apresenta informações pessoais nas seguintes instâncias-chave no serviço:

- **Definições de notificação** – Quando cria uma encomenda, pode configurar o endereço de e-mail dos utilizadores nas definições de notificação. Estas informações podem ser visualizadas pelo administrador. Estas informações são eliminadas pelo serviço quando a tarefa atinge o estado terminal ou ao eliminar a encomenda.

- **Detalhes da encomenda** – Depois de criada a encomenda, o endereço de envio, o e-mail e as informações de contacto dos utilizadores são armazenados no portal do Azure. As informações guardadas incluem:

  - Nome do contacto
  - Número de telefone
  - Email
  - Morada
  - Localidade
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

|Diretriz   |Descrição   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | Para proteção contra água e poeira         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | Para resistir a condições de transporte adversas          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | Para a atualização de firmware seguro         |
|[FIPS 140-2 Level 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | Para proteção de dados         |
|Apêndice A, para unidades de disco rígido ATA em [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | Para a limpeza de dados         |

## <a name="next-steps"></a>Passos seguintes

- Reveja os [Requisitos do Data Box](data-box-system-requirements.md).
- Compreenda os [limites do Data Box](data-box-limits.md).
- Implemente rapidamente o [Azure Data Box](data-box-quickstart-portal.md) no portal do Azure.

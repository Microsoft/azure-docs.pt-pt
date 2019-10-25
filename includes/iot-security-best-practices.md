---
title: incluir ficheiro
description: incluir ficheiro
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4fdb891d668d99644d8a9ed9c15d158e65d53ba5
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793140"
---
Proteger uma infraestrutura de Internet das Coisas (IoT) exige uma rigorosa estratégia de segurança aprofundada. Essa estratégia exige que você proteja os dados na nuvem, proteja a integridade dos dados enquanto estiver em trânsito pela Internet pública e provisione dispositivos com segurança. Cada camada cria uma garantia de segurança maior na infraestrutura geral.

## <a name="secure-an-iot-infrastructure"></a>Proteger uma infraestrutura de IoT

Essa estratégia de segurança aprofundada pode ser desenvolvida e executada com a participação ativa de vários jogadores envolvidos na fabricação, no desenvolvimento e na implantação de dispositivos e infraestruturas IoT. Veja a seguir uma descrição de alto nível desses players.

* **Fabricante/integrador de hardware IOT**: normalmente, esses players são os fabricantes de hardware IOT que estão sendo implantados, integradores montando hardware de vários fabricantes ou fornecedores fornecendo hardware para uma implantação de IOT fabricada ou integrado por outros fornecedores.

* **Desenvolvedor de soluções de IOT**: o desenvolvimento de uma solução de IOT normalmente é feito por um desenvolvedor de soluções. Esse desenvolvedor pode fazer parte de uma equipe interna ou um SI (integrador de sistema) especializado nessa atividade. O desenvolvedor da solução de IoT pode desenvolver vários componentes da solução de IoT do zero, integrar vários componentes prontos para a prateleira ou de software livre ou adotar aceleradores de solução com uma pequena adaptação.

* **Implantador de solução de IOT**: depois que uma solução de IOT é desenvolvida, ela precisa ser implantada no campo. Esse processo envolve a implantação de hardware, a interconexão de dispositivos e a implantação de soluções em dispositivos de hardware ou na nuvem.

* **Operador de solução de IOT**: depois que a solução de IOT for implantada, ela exigirá operações de longo prazo, monitoramento, atualizações e manutenção. Essas tarefas podem ser feitas por uma equipe interna que consiste em especialistas em tecnologia da informação, em operações de hardware e em equipes de manutenção e em especialistas de domínio que monitoram o comportamento correto da infraestrutura de IoT geral.

As seções a seguir fornecem as práticas recomendadas para cada um desses players para ajudar a desenvolver, implantar e operar uma infraestrutura de IoT segura.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Veja a seguir as práticas recomendadas para fabricantes de hardware IoT e integradores de hardware.

* **Escopo de hardware para requisitos mínimos**: o design de hardware deve incluir os recursos mínimos necessários para a operação do hardware e nada mais. Um exemplo é incluir portas USB somente se necessário para a operação do dispositivo. Esses recursos adicionais abrem o dispositivo para vetores de ataque indesejados que devem ser evitados.

* **Faça a prova de adulteração do hardware**: Crie mecanismos para detectar a violação física, como a abertura da capa do dispositivo ou a remoção de uma parte do dispositivo. Esses sinais de violação podem fazer parte do fluxo de dados carregado para a nuvem, o que pode alertar os operadores desses eventos.

* Crie com base em **hardware seguro**: se o Cogs permitir, crie recursos de segurança, como armazenamento seguro e criptografado, ou funcionalidade de inicialização baseada em Trusted Platform Module (TPM). Esses recursos tornam os dispositivos mais seguros e ajudam a proteger a infraestrutura geral da IoT.

* **Tornar as atualizações seguras**: as atualizações de firmware durante o tempo de vida do dispositivo são inevitáveis. A criação de dispositivos com caminhos seguros para atualizações e a garantia criptográfica de versões de firmware permitirá que o dispositivo seja seguro durante e após as atualizações.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções de IoT

Veja a seguir as práticas recomendadas para desenvolvedores de soluções de IoT:

* **Siga a metodologia de desenvolvimento de software seguro**: o desenvolvimento de software seguro exige um raciocínio de ponta sobre a segurança, desde a concepção do projeto até sua implementação, teste e implantação. As opções de plataformas, linguagens e ferramentas são todas influenciadas com essa metodologia. O ciclo de vida de desenvolvimento de segurança da Microsoft fornece uma abordagem passo a passo para a criação de software seguro.

* **Escolha software livre com cuidado**: o software livre oferece uma oportunidade de desenvolver soluções rapidamente. Ao escolher software livre, considere o nível de atividade da Comunidade para cada componente de código-fonte aberto. Uma comunidade ativa garante que o software tenha suporte e que os problemas sejam descobertos e resolvidos. Como alternativa, um projeto de software livre obscuro e inativo pode não ter suporte e os problemas provavelmente não são descobertos.

* **Integre com cuidado**: existem muitas falhas de segurança de software no limite de bibliotecas e APIs. A funcionalidade que pode não ser necessária para a implantação atual ainda pode estar disponível por meio de uma camada de API. Para garantir a segurança geral, certifique-se de verificar todas as interfaces de componentes que estão sendo integrados a falhas de segurança.

## <a name="iot-solution-deployer"></a>Implantador de solução de IoT

A seguir estão as práticas recomendadas para implantadores de solução de IoT:

* **Implantar hardware com segurança**: as implantações de IOT podem exigir que o hardware seja implantado em locais não seguros, como em espaços públicos ou localidades não supervisionadas. Nessas situações, certifique-se de que a implantação de hardware seja à prova de adulteração até a extensão máxima. Se USB ou outras portas estiverem disponíveis no hardware, verifique se elas estão cobertas com segurança. Muitos vetores de ataque podem usá-los como pontos de entrada.

* **Mantenha as chaves de autenticação seguras**: durante a implantação, cada dispositivo requer IDs de dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha essas chaves fisicamente seguras mesmo após a implantação. Qualquer chave comprometida pode ser usada por um dispositivo mal-intencionado para se disfarçar como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução de IoT

A seguir estão as práticas recomendadas para operadores de solução de IoT:

* **Manter o sistema atualizado**: Verifique se os sistemas operacionais do dispositivo e todos os drivers de dispositivo foram atualizados para as versões mais recentes. Se você ativar as atualizações automáticas no Windows 10 (IoT ou outras SKUs), a Microsoft a manterá atualizada, fornecendo um sistema operacional seguro para dispositivos IoT. Manter outros sistemas operacionais (como o Linux) ajuda a garantir que eles também sejam protegidos contra ataques mal-intencionados.

* **Proteger contra atividades mal-intencionadas**: se o sistema operacional permitir, instale os recursos antivírus e antimalware mais recentes em cada sistema operacional do dispositivo. Essa prática pode ajudar a reduzir a maioria das ameaças externas. Você pode proteger os sistemas operacionais mais modernos contra ameaças executando as etapas apropriadas.

* **Auditar frequentemente**: a auditoria da infraestrutura de IOT para problemas relacionados à segurança é a chave ao responder a incidentes de segurança. A maioria dos sistemas operacionais fornece log de eventos interno que deve ser revisado com frequência para garantir que nenhuma violação de segurança tenha ocorrido. As informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço de nuvem onde ele pode ser analisado.

* **Proteger fisicamente a infraestrutura de IOT**: os piores ataques de segurança contra a infraestrutura de IOT são iniciados usando o acesso físico aos dispositivos. Uma prática de segurança importante é proteger contra o uso mal-intencionado de portas USB e de outros acessos físicos. Uma chave para descobrir violações que podem ter ocorrido é o registro em log de acesso físico, como o uso de porta USB. Novamente, o Windows 10 (IoT e outros SKUs) permite o registro detalhado desses eventos.

* **Proteger credenciais de nuvem**: as credenciais de autenticação de nuvem usadas para configurar e operar uma implantação de IOT possivelmente são a maneira mais fácil de obter acesso e comprometer um sistema de IOT. Proteja as credenciais alterando a senha com frequência e evite usar essas credenciais em máquinas públicas.

Os recursos de diferentes dispositivos IoT variam. Alguns dispositivos podem ser computadores que executam sistemas operacionais de desktop comuns e alguns dispositivos podem estar executando sistemas operacionais muito leves. As práticas recomendadas de segurança descritas anteriormente podem ser aplicáveis a esses dispositivos em graus variados. Se fornecidas, as práticas recomendadas de segurança e implantação adicionais dos fabricantes desses dispositivos devem ser seguidas.

Alguns dispositivos herdados e restritos podem não ter sido projetados especificamente para implantação de IoT. Esses dispositivos podem não ter a capacidade de criptografar dados, conectar-se à Internet ou fornecer auditoria avançada. Nesses casos, um gateway de campo moderno e seguro pode agregar dados de dispositivos herdados e fornecer a segurança necessária para conectar esses dispositivos pela Internet. Os gateways de campo podem fornecer autenticação segura, negociação de sessões criptografadas, recebimento de comandos da nuvem e muitos outros recursos de segurança.
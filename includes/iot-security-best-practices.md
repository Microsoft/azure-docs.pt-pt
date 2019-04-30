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
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442689"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Melhores práticas de segurança para Internet das coisas (IoT)

A proteção de uma infra-estrutura de Internet das coisas (IoT) exige uma estratégia de segurança em profundidade rigorosa. Esta estratégia requer a proteger os dados na cloud, proteger a integridade dos dados em trânsito através da internet pública e aprovisionar os dispositivos de forma segura. Cada camada baseia-se maior garantia de segurança numa infraestrutura global.

## <a name="secure-an-iot-infrastructure"></a>Proteger uma infraestrutura de IoT

Esta estratégia de segurança em profundidade pode ser desenvolvida e executada com o Active Directory participação de vários jogadores envolvidos com o fabrico, desenvolvimento e implementação de dispositivos IoT e infra-estrutura. Segue-se uma descrição de alto nível destes jogadores.

* **Fabricante de hardware de IoT/integrador**: Normalmente, estes jogadores são os fabricantes de hardware de IoT que está a ser implementado, integradores de montagem de hardware de vários fabricantes, ou os fornecedores fornecendo o hardware para uma implementação de IoT fabricados ou integrados por outros fornecedores.

* **Desenvolvedor de soluções de IoT**: O desenvolvimento de uma solução de IoT é geralmente feito por um desenvolvedor de soluções. Este developer pode ser parte de uma equipa interna ou um integrador de sistemas (SI) especializado nesta atividade. O desenvolvedor de soluções de IoT pode desenvolver vários componentes da solução IoT partir do zero, integrar vários componentes comerciais ou de código-fonte aberto ou adotar Aceleradores de solução com a adaptação de pequenas.

* **Implementador de solução de IoT**: Depois de uma solução de IoT é desenvolvida, ele precisa ser implantado no campo. Este processo envolve a implementação de hardware, interconexão de dispositivos e a implantação de soluções em dispositivos de hardware ou na cloud.

* **Operador de solução de IoT**: Depois de implementar a solução de IoT, necessita de operações de longa duração, monitorização, atualizações e manutenção. Estas tarefas podem ser feitas por uma equipe interna que é composto por especialistas em tecnologia de informação, as operações de hardware e as equipes de manutenção e especialistas de domínio que monitorar o comportamento correto de infraestrutura de IoT global.

As secções que se seguem fornecem as práticas recomendadas para cada um desses jogadores para ajudar a desenvolver, implantar e operar uma infraestrutura IoT segura.

## <a name="iot-hardware-manufacturerintegrator"></a>Fabricante/integrador de hardware de IoT

Seguem-se as práticas recomendadas para fabricantes de hardware de IoT e integradores de hardware.

* **Definir o âmbito de requisitos mínimos de hardware**: O design de hardware deve incluir os recursos mínimos necessários para a operação de hardware e nada mais. Um exemplo é incluir portas USB apenas se for necessário para a operação do dispositivo. As seguintes funcionalidades adicionais, abra o dispositivo de vetores de ataque indesejados que deve ser evitado.

* **Torna o hardware à prova**: Crie em mecanismos para detetar a adulteração físico, como abrir da capa de dispositivo ou remover uma parte do dispositivo. Esses sinais de adulteração pode ser parte do fluxo de dados carregado para a nuvem, o que poderia alertar os operadores desses eventos.

* **Criar em todo o hardware de segurança**: Se COGS permite, crie recursos de segurança, como o armazenamento seguro e encriptado ou funcionalidade de arranque com base no Trusted Platform Module (TPM). Esses recursos tornam mais seguro e ajudar a proteger a infraestrutura de IoT global de dispositivos.

* **Fazer com que as atualizações seguras**: Atualizações de firmware durante o tempo de vida do dispositivo são inevitáveis. Criação de dispositivos com caminhos de seguros para as atualizações e garantia de criptografia de versões de firmware permitirá que o dispositivo esteja seguro durante e após as atualizações.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções de IoT

Seguem-se as práticas recomendadas para os desenvolvedores de solução de IoT:

* **Siga a metodologia de desenvolvimento de software seguro**: Desenvolvimento de software seguro requer início pensar sobre a segurança, desde o início do projeto até à sua implementação, teste e implantação. As opções de plataformas, linguagens e ferramentas são influenciadas com essa metodologia. O ciclo de vida do desenvolvimento de segurança do Microsoft fornece uma abordagem passo a passo para criar um software seguro.

* **Escolha o software de código-fonte aberto com cuidado**: Software open-source fornece uma oportunidade para desenvolver soluções rapidamente. Quando está escolhendo o software de código-fonte aberto, considere o nível de atividade da Comunidade para cada componente de código-fonte aberto. Uma Comunidade ativa garante que o software é suportado e que os problemas são detetados e resolvidos. Em alternativa, um projeto de software de código-fonte aberto obscuras e inativos pode não ser suportado e problemas provavelmente não são ser detetados.

* **Integrar com cuidado**: Existem muitas falhas de segurança de software no limite do bibliotecas e APIs. Funcionalidade que talvez não sejam necessária para a implementação atual ainda pode estar disponível através de uma camada de API. Para garantir a segurança geral, lembre-se de que todas as interfaces de componentes a ser integrados para falhas de segurança de verificação.

## <a name="iot-solution-deployer"></a>Implementador de solução de IoT

Seguem-se as práticas recomendadas para implementadores de solução de IoT:

* **Implementar hardware de forma segura**: Implementações de IoT podem exigir hardware para ser implantado em localizações não seguras, como espaços públicos ou localidades não supervisionadas. Em tais situações, certifique-se de que a implantação de hardware é à prova de até ao limite máximo. Se o USB ou de outras portas estão disponíveis no hardware, certifique-se de que eles são explicados em segurança. Muitos vetores de ataque podem utilizá-los como pontos de entrada.

* **Manter as chaves de autenticação seguro**: Durante a implementação, cada dispositivo necessita de IDs de dispositivo e as chaves de autenticação associado geradas pelo serviço em nuvem. Manter essas chaves fisicamente protegidos, mesmo após a implementação. Qualquer chave comprometida pode ser utilizado por um dispositivo de mal-intencionado se disfarçasse como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução de IoT

Seguem-se as práticas recomendadas para os operadores de solução de IoT:

* **Manter o sistema atualizado**: Certifique-se de que os sistemas operativos de dispositivos e todos os drivers de dispositivo são atualizados para as versões mais recentes. Se ativar as atualizações automáticas no Windows 10 (IoT ou outros SKU), Microsoft mantém-lo atualizado, fornecendo um sistema operativo seguro para dispositivos de IoT. Manter os outros sistemas operacionais (por exemplo, o Linux) ajuda atualizada Certifique-se de que também estão protegidos contra ataques mal-intencionados.

* **Proteger contra atividades maliciosas**: Se permite que o sistema operacional, instale as funcionalidades mais recentes de antivírus e antimalware em cada sistema operativo do dispositivo. Esta prática pode ajudar a atenuar as ameaças mais externas. Pode proteger os sistemas de operativos mais modernos contra ameaças ao realizar os passos adequados.

* **Auditoria com frequência**: Infraestrutura de IoT para problemas relacionados com a segurança de auditoria é a chave em resposta a incidentes de segurança. A maioria dos sistemas operativos fornecem o log de eventos internos que deve ser revista com frequência para se certificar de que nenhuma violação de segurança ocorreu. Informações de auditoria podem ser enviadas como um fluxo de telemetria separado para o serviço em nuvem em que podem ser analisados.

* **Proteger fisicamente a infraestrutura de IoT**: Os pior ataques de segurança em relação a infraestrutura IoT são iniciados com acesso físico aos dispositivos. É uma prática de segurança importante proteger contra uso mal-intencionado de portas USB e outro acesso físico. Uma chave a descoberta de falhas que possam ter ocorrido é o registo de acesso físico, como a utilização de porta USB. Novamente, o Windows 10 (IoT e outros SKU) ativa o registo detalhado desses eventos.

* **Proteger credenciais de cloud**: Credenciais de autenticação em nuvem utilizadas para configurar e operar uma implementação de IoT são possivelmente a maneira mais fácil de obter acesso e comprometer um sistema de IoT. Proteger as credenciais ao alterar a palavra-passe com frequência e de não utilizar estas credenciais em computadores públicos.

Recursos de diferentes dispositivos de IoT variam de acordo. Alguns dispositivos podem ser computadores com sistemas de operativos de ambiente de trabalho comuns e alguns dispositivos podem estar a executar sistemas de operativos muito leves. As práticas recomendadas de segurança descritas anteriormente podem ser aplicáveis a estes dispositivos em vários graus. Se for fornecido, segurança e implementação de melhores práticas adicionais de fabricantes desses dispositivos devem ser seguidas.

Alguns dispositivos legados e restritos poderão não ter sido projetados especificamente para a implementação de IoT. Estes dispositivos podem não ter a capacidade de criptografar dados, ligue-se com a Internet ou forneça auditoria avançada. Nestes casos, um gateway de campo moderna e segura pode agregar dados a partir de dispositivos legados e fornecer a segurança necessária para ligar estes dispositivos através da Internet. Gateways de campo podem fornecer uma autenticação segura, a negociação de sessões encriptados, a receção dos comandos da nuvem e muitas outras funcionalidades de segurança.
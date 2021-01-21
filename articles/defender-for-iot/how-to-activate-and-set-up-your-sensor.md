---
title: Ativar e configurar o seu sensor
description: Este artigo descreve como iniciar s dia e ativar uma consola de sensores.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 908460bd0a034e21524b6ea6d3042f362cc810d4
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623594"
---
# <a name="activate-and-set-up-your-sensor"></a>Ativar e configurar o seu sensor

Este artigo descreve como ativar um sensor e executar a configuração inicial.

Os utilizadores de administrador efetuam a ativação ao iniciar a sua inscrição pela primeira vez e quando é necessária uma gestão de ativação. A configuração garante que o sensor está configurado para detetar e alertar da melhor forma.

Analistas de segurança e utilizadores apenas de leitura não podem ativar um sensor ou gerar uma nova palavra-passe.

## <a name="sign-in-and-activation-for-administrator-users"></a>Iniciar sação e ativação para utilizadores de administradores

Os administradores que iniciarem seduca pela primeira vez devem verificar se têm acesso a ficheiros de ativação e recuperação de passwords que foram descarregados durante o sensor a bordo. Caso contrário, necessitam de permissões de administrador de segurança Azure, colaborador de subscrição ou proprietário de subscrição para gerar estes ficheiros no portal Azure Defender para IoT.

### <a name="first-time-sign-in-and-activation-checklist"></a>Lista de verificação de entrada e ativação pela primeira vez

Antes de iniciar sessão na consola de sensores, os utilizadores do administrador devem ter acesso a:

- O endereço IP do sensor que foi definido durante a instalação.

- Credenciais de entrada do utilizador para o sensor. Se descarregou um ISO para o sensor, utilize as credenciais predefinidas que recebeu durante a instalação. Recomendamos que crie um novo utilizador *administrador* após a ativação.

- Uma senha inicial. Se adquiriu um sensor pré-configurado a partir de Arrow, tem de gerar uma palavra-passe ao iniciar sessão pela primeira vez.

- O ficheiro de ativação associado a este sensor. O ficheiro foi gerado e descarregado durante o sensor a bordo do portal Defender para IoT.

- Um certificado assinado pela SSL/TLS CA que a sua empresa necessita.

### <a name="about-activation-files"></a>Sobre ficheiros de ativação

O seu sensor foi a bordo do Azure Defender para IoT num modo de gestão específico:

| Tipo de modo | Descrição |
|--|--|
| **Modo ligado à nuvem** | A informação que o sensor deteta é exibida na consola do sensor. As informações de alerta também são entregues através do hub IoT e podem ser partilhadas com outros serviços da Azure, como o Azure Sentinel. |
| **Modo ligado localmente** | A informação que o sensor deteta é exibida na consola do sensor. As informações de deteção também são partilhadas com a consola de gestão no local, se o sensor estiver ligado à sua. |

Um ficheiro de ativação ligado localmente ou ligado à nuvem foi gerado e descarregado para este sensor durante o embarque. O ficheiro de ativação contém instruções para o modo de gestão do sensor. *Um ficheiro de ativação único deve ser carregado para cada sensor que implementar.*  Na primeira vez que iniciar sina, tem de carregar o ficheiro de ativação relevante para este sensor.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-activation-file-download-button.png" alt-text="Azure Defender para portal IoT, sensor de bordo.":::

### <a name="about-certificates"></a>Acerca de certificados

Após a instalação do sensor, é gerado um certificado auto-assinado local e utilizado para aceder à consola de sensores. Depois de um administrador entrar na consola pela primeira vez, esse utilizador é solicitado a bordo de um certificado SSL/TLS.

Estão disponíveis dois níveis de segurança:

- Satisfaça os requisitos específicos de certificado e encriptação solicitados pela sua organização, através do upload do certificado assinado pela AC.
- Permitir validação entre a consola de gestão e os sensores ligados. A validação é avaliada com uma lista de revogação de certificados e a data de validade do certificado. *Se a validação falhar, a comunicação entre a consola de gestão e o sensor é interrompida e aparece um erro de validação na consola.* Esta opção é ativada por padrão após a instalação.  

A consola suporta os seguintes tipos de certificados:

- Infraestrutura chave privada e empresarial (PKI privado)

- Infraestruturas de chaves públicas (PKI público)

- Gerado localmente no aparelho (auto-assinado localmente) 

  > [!IMPORTANT]
  > Recomendamos que não utilize o certificado auto-assinado por defeito. O certificado não é seguro e deve ser utilizado apenas para ambientes de ensaio. O dono do certificado não pode ser validado, e a segurança do seu sistema não pode ser mantida. Nunca utilize esta opção para redes de produção.

### <a name="sign-in-and-activate-the-sensor"></a>Iniciar s.ção e ativar o sensor

Para iniciar sins e ativar:

1. Aceda à consola de sensores do seu navegador utilizando o IP definido durante a instalação. A caixa de diálogo de entrada abre.

    :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-sensor-log-in-screen.png" alt-text="Azure Defender para sensor IoT.":::

1. Introduza as credenciais definidas durante a instalação do sensor. Se adquiriu um sensor pré-configurado a partir de Arrow, gere primeiro uma palavra-passe. Para obter mais informações sobre a recuperação da palavra-passe, consulte [investigar a falha da palavra-passe no início da sindução](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md#investigate-password-failure-at-initial-sign-in).

1. Depois de iniciar sação, a caixa de diálogo de **ativação** abre-se. Selecione **Upload** e vá para o ficheiro de ativação que descarregou durante o sensor a bordo.

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/activation-upload-screen-with-upload-button.png" alt-text="Selecione Upload e vá para o ficheiro de ativação.":::

1. Selecione a **ligação de configuração da rede de sensores** se pretender alterar a configuração da rede do sensor antes da ativação. Consulte [a configuração da rede do sensor de atualização antes da ativação](#update-sensor-network-configuration-before-activation).

1. Aceite os termos e condições.

1. **Selecione Ativar**. Abre-se a caixa de diálogo do certificado SSL/TLS.

1. Defina um nome de certificado.
1. Faça o upload do CRT e dos ficheiros chave.
1. Introduza uma palavra-passe e carreme um ficheiro PEM, se necessário.
1. Selecione **Seguinte**. O ecrã de validação abre. Por predefinição, a validação entre a consola de gestão e os sensores ligados está ativada.
1. Desligue a **validação** de toda a forma do sistema para desativar a validação. Recomendamos que permita a validação.
1. Selecione **Guardar**.  

Poderá ter de refrescar o ecrã depois de carregar o certificado assinado pela AC.

Para obter informações sobre o upload de um novo certificado, parâmetros de certificado suportados e trabalhar com comandos de certificado CLI, consulte [Gerir sensores individuais](how-to-manage-individual-sensors.md).

#### <a name="update-sensor-network-configuration-before-activation"></a>Atualizar a configuração da rede de sensores antes da ativação  

Os parâmetros de configuração da rede do sensor foram definidos durante a instalação do software ou quando adquiriu um sensor pré-configurado. Foram definidos os seguintes parâmetros:

- Endereço IP
- DNS
- Gateway predefinido
- Máscara de sub-rede
- Nome do anfitrião

É melhor atualizar esta informação antes de ativar o sensor. Por exemplo, pode ser necessário alterar os parâmetros pré-configurados definidos por Seta. Também pode definir definições de procuração antes de ativar o seu sensor.

Para atualizar os parâmetros de configuração da rede de sensores:

1. Selecione a **ligação de configuração da rede de sensores** formar a caixa de diálogo de **ativação.**

   :::image type="content" source="media/how-to-activate-and-set-up-your-sensor/editable-network-configuration-screen-v2.png" alt-text="Configuração da rede de sensores.":::

2. Os parâmetros definidos durante a instalação são apresentados. A opção de definir o proxy também está disponível. Atualize as definições necessárias e **selecione Guardar**.

### <a name="subsequent-sign-ins"></a>Insusões subsequentes

Após a primeira ativação, a consola de sensores Azure Defender para IoT abre após a entrada sem necessitar de um ficheiro de ativação. Só precisas das tuas credenciais de inscrição.

Depois da sua sação, abre o Azure Defender para a consola IoT.

:::image type="content" source="media/how-to-activate-and-set-up-your-sensor/azure-defender-for-iot-log-in-screen-dashboard-v2.png" alt-text="Azure Defender para consola IoT.":::

## <a name="initial-setup-and-learning-for-administrators"></a>Configuração e aprendizagem iniciais (para administradores)

Após o seu primeiro início de sação, o sensor Azure Defender para IoT começa a monitorizar automaticamente a sua rede. Os dispositivos de rede aparecerão nas secções de inventário do mapa do dispositivo e do dispositivo. O Azure Defender for IoT começará a detetá-lo e alertá-lo sobre todos os incidentes de segurança e operacionais que ocorram na sua rede. Em seguida, pode criar relatórios e consultas com base nas informações detetadas.

Inicialmente esta atividade é realizada no modo de aprendizagem, que instrui o seu sensor a aprender a atividade habitual da sua rede. Por exemplo, o sensor aprende dispositivos descobertos na sua rede, protocolos detetados na rede e transferências de ficheiros que ocorrem entre dispositivos específicos. Esta atividade torna-se a atividade de base da sua rede.

### <a name="review-and-update-basic-system-settings"></a>Rever e atualizar as definições básicas do sistema

Reveja as definições do sistema do sensor para se certificar de que o sensor está configurado para detetar e alertar da melhor forma.

Defina as definições do sistema do sensor. Por exemplo:

- Defina sub-redes ICS (ou IoT) e sub-redes segregadas.

- Defina pseudónimos portuários para protocolos específicos do site.

- Defina VLANs e nomes que estão em uso.

- Se o DHCP estiver a ser utilizado, defina gamas DHCP legítimas.

- Defina integração com o Ative Directory e servidores de correio.

### <a name="disable-learning-mode"></a>Desativar o modo de aprendizagem

Depois de ajustar as definições do sistema, pode deixar o Azure Defender para o sensor IoT funcionar no modo de aprendizagem até sentir que as deteções do sistema refletem com precisão a sua atividade na rede.

O modo de aprendizagem deve funcionar durante cerca de 2 a 6 semanas, dependendo do tamanho e complexidade da sua rede. Depois de desativar o modo de aprendizagem, qualquer atividade que difere da sua atividade de base irá desencadear um alerta.

Para desativar o modo de aprendizagem:

- Selecione **Definições do Sistema** e desligue a opção **Aprendizagem.**

## <a name="first-time-sign-in-for-security-analysts-and-read-only-users"></a>Insusição pela primeira vez para analistas de segurança e utilizadores apenas de leitura

Antes de iniciar sôm, verifique se tem:

- O endereço IP do sensor.
- Credenciais de inscrição que o seu administrador forneceu.

## <a name="console-tools-overview"></a>Ferramentas de consola: Visão geral

Acede às ferramentas de consola do menu lateral.

**Navegação** 

| Janela | Ícone | Descrição |
| -----------|--|--|
| Dashboard | :::image type="icon" source="media/concept-sensor-console-overview/dashboard-icon-azure.png" border="false"::: | Veja uma imagem intuitiva do estado de segurança da rede. |
| Mapa do dispositivo | :::image type="icon" source="media/concept-sensor-console-overview/asset-map-icon-azure.png" border="false"::: | Veja os dispositivos de rede, as ligações do dispositivo e as propriedades do dispositivo num mapa. Várias opções de zooms, destaque e filtro estão disponíveis para exibir a sua rede. |
| Inventário de dispositivos | :::image type="icon" source="media/concept-sensor-console-overview/asset-inventory-icon-azure.png" border="false":::  | O inventário do dispositivo apresenta uma vasta gama de atributos do dispositivo que este sensor deteta. As opções estão disponíveis para: <br /> - Filtrar as informações de acordo com os campos de mesa e ver as informações filtradas apresentadas. <br /> - Informação de exportação para um ficheiro CSV. <br /> - Importar detalhes do registo do Windows.|
| Alertas | :::image type="icon" source="media/concept-sensor-console-overview/alerts-icon-azure.png" border="false"::: | Os alertas de exibição quando ocorrem violações de política, ocorrem desvios do comportamento da linha de base ou qualquer tipo de atividade suspeita na rede. |
| Relatórios | :::image type="icon" source="media/concept-sensor-console-overview/reports-icon-azure.png" border="false"::: | Ver relatórios que são baseados em consultas de mineração de dados. |

**Análise**

| Janela| Ícone | Descrição |
|---|---|---|
| Cronologia do evento | :::image type="icon" source="media/concept-sensor-console-overview/event-timeline-icon-azure.png" border="false"::: | Ver uma linha temporal com informações sobre alertas, eventos de rede (informativos) e operações de utilizador, tais como ins- ins de utilizador e eliminações de utilizadores.|

**Navegação**

| Janela | Ícone | Descrição |
|---|---|---|
| Mineração de dados | :::image type="icon" source="media/concept-sensor-console-overview/data-mining-icon-azure.png" border="false"::: | Gere informação abrangente e granular sobre os dispositivos da sua rede em várias camadas. |
| Tendências e estatísticas | :::image type="icon" source="media/concept-sensor-console-overview/trends-and-statistics-icon-azure.jpg" border="false"::: | Ver tendências e estatísticas numa vasta gama de widgets. |
| Avaliação de Riscos | :::image type="icon" source="media/concept-sensor-console-overview/vulnerabilities-icon-azure.png" border="false"::: | Exiba a janela **Vulnerabilidades.** |

**Administração**

| Janela | Ícone | Descrição |
|---|---|---|
| Utilizadores | :::image type="icon" source="media/concept-sensor-console-overview/users-icon-azure.png" border="false"::: | Defina utilizadores e funções com vários níveis de acesso. |
| Encaminhamento | :::image type="icon" source="media/concept-sensor-console-overview/forwarding-icon-azure.png" border="false"::: | Encaminhar informações de alerta para parceiros que integram com o Defender para IoT, para endereços de e-mail, para servidores webhook, e muito mais. <br /> Consulte [as informações do alerta para](how-to-forward-alert-information-to-partners.md) a frente para mais detalhes. |
| Definições do sistema | :::image type="icon" source="media/concept-sensor-console-overview/system-settings-icon-azure.png" border="false"::: | Configure as definições do sistema. Por exemplo, definir as definições de DHCP, fornecer detalhes do servidor de correio ou criar pseudónimos de porta. |
| Definições de importação | :::image type="icon" source="media/concept-sensor-console-overview/import-settings-icon-azure.png" border="false"::: | Exiba a janela **'Definições de Importação'.** Pode efetuar alterações manuais nas informações de um dispositivo.<br /> Consulte [as informações do dispositivo de importação](how-to-import-device-information.md) para obter mais informações. |

**Suporte**

| Janela| Ícone | Descrição |
|----|---|---|
| Suporte | :::image type="icon" source="media/concept-sensor-console-overview/support-icon-azure.png" border="false"::: | Contacte [o Microsoft Support](https://support.microsoft.com/) para obter ajuda. |

### <a name="see-also"></a>Ver também

[A bordo de um sensor](getting-started.md#4-onboard-a-sensor)

[Gerir ficheiros de ativação de sensores](how-to-manage-individual-sensors.md#manage-sensor-activation-files)

[Controlar que tráfego é monitorizado](how-to-control-what-traffic-is-monitored.md)

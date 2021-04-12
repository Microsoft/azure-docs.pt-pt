---
title: Configurar a monitorização MIB do SNMP
description: Pode efetuar a monitorização da saúde dos sensores utilizando o SNMP. O sensor responde a consultas SNMP enviadas a partir de um servidor de monitorização autorizado.
ms.date: 12/14/2020
ms.topic: how-to
ms.openlocfilehash: 1ba52236f65c6c5daba68c67677cdc6adfb699b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781675"
---
# <a name="set-up-snmp-mib-monitoring"></a>Configurar a monitorização MIB do SNMP

Pode efetuar a monitorização de saúde dos sensores utilizando o Simple Network Management Protocol (SNMP). O sensor responde a consultas SNMP enviadas a partir de um servidor de monitorização autorizado. O SNMP monitoriza as sondagens do sensor OIDs periodicamente (até 50 vezes por segundo).

As versões suportadas pelo SNMP são SNMP v2 ou SNMP v3. O SNMP utiliza a UDP como protocolo de transporte com o porto 161 (SNMP).

Antes de começar a configurar a monitorização SNMP, tem de abrir o UDP 161 na firewall.

## <a name="oids"></a>OIDs

| Consola de gestão e sensor | OID | Formato | Descrição |
|--|--|--|--|
| Nome do aparelho | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | Nome do aparelho para a consola de gestão no local |
| Fornecedor | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Suporte microsoft (support.microsoft.com) |
| Plataforma | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Consola de gestão de sensores ou no local |
| Número de série | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | Corda que a licença usa |
| Versão de software | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Cadeia de versão completa Xsense e gestão de versão completa |
| Utilização da CPU | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Indicação para zero a 100 |
| Temperatura do CPU | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Indicação celsius para zero a 100 com base na entrada linux |
| Utilização de memória | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Indicação para zero a 100 |
| Utilização do disco | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Indicação para zero a 100 |
| Estado do Serviço | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Online ou offline se um dos quatro componentes cruciais estiver em baixo |
| Largura de banda | Fora de alcance para 2.4 |  | A largura de banda recebida em cada interface de monitor no Xsense |

   - As teclas não existentes respondem com nulo, HTTP 200, com base no [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - Os MIBs relacionados com hardware (utilização de CPU, temperatura do CPU, utilização da memória, utilização do disco) devem ser testados em todas as arquiteturas e sensores físicos. Espera-se que a temperatura da CPU em máquinas virtuais não seja aplicável.

Pode descarregar o registo que contém todas as consultas SNMP que o sensor recebe, incluindo os dados de ligação e dados brutos do pacote.

Para definir a monitorização sanitária SNMP v2:

1. No menu lateral, selecione **Definições do Sistema**.

2. No painel **Ative Discovery,** selecione **SNMP MIB Monitoring** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Edite a sua janela SNMP.":::

3. Na secção **Hostes Permitidos,** selecione **Adicionar anfitrião** e insira o endereço IP do servidor que executa a monitorização de saúde do sistema.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Insira o endereço IP para anfitriões autorizados.":::

4. Na secção **autenticação,** na caixa **de cordas comunitária SNMP v2,** introduza a cadeia. A cadeia comunitária SNMP pode conter até 32 caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). Não são permitidos espaços.

5. Selecione **Guardar**.

Para definir a monitorização sanitária SNMP v3:

1. No menu lateral, selecione **Definições do Sistema**.

2. No painel **Ative Discovery,** selecione **SNMP MIB Monitoring** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: .

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Edite a sua janela SNMP.":::

3. Na secção **Hostes Permitidos,** selecione **Adicionar anfitrião** e insira o endereço IP do servidor que executa a monitorização de saúde do sistema.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Insira o endereço IP para os anfitriões autorizados.":::

4. Na secção de **Autenticação,** definir os seguintes parâmetros:

    | Parâmetro | Descrição |
    |--|--|
    | **Nome de Utilizador** | O nome de utilizador do SNMP pode conter até 32 caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). Não são permitidos espaços. <br /> <br />O nome de utilizador da autenticação V3 do SNMP deve ser configurado no sistema e no servidor SNMP. |
    | **Palavra-passe** | Introduza uma senha de autenticação sensível a casos. A palavra-passe de autenticação pode conter 8 a 12 caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). <br /> <br/>O nome de utilizador da autenticação V3 do SNMP deve ser configurado no sistema e no servidor SNMP. |
    | **Tipo de Autenticação** | Selecione MD5 ou SHA. |
    | **Encriptação** | Selecione DES ou AES. |
    | **Chave Secreta** | A chave deve conter exatamente oito caracteres e incluir qualquer combinação de caracteres alfanuméricos (letras maiúsculas, letras minúsculas e números). |

5. Selecione **Guardar**.

## <a name="see-also"></a>Ver também

[Registos de resolução de problemas de exportação](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)

# Evidências do protótipo — Tomada Vital

Esta página reúne a montagem original e **cinco capturas dos testes com o código Arduino 2.0**, feitas no Tinkercad em 18/11/2026. Os resultados abaixo são extraídos diretamente das capturas e descrevem apenas o que foi observado.

## Circuito compartilhado no Tinkercad

**[Abrir a simulação original da Tomada Vital](https://www.tinkercad.com/things/g958jGWu1qs-fantabulous-bruticus-kup/editel?returnTo=%2Fthings%2Fg958jGWu1qs-fantabulous-bruticus-kup&sharecode=o65KUUcEojXKD6Cn6BC4HaEw2s_Gv3T1Dquyp1OA2sM)**

O link abre a nossa montagem para acompanhar as evidências abaixo e tentar reproduzir os testes. O código-fonte também está disponível na [raiz do repositório](../tomada_vital.ino).

## 01 — Circuito geral

<a href="./01-circuito-geral.png"><img src="./01-circuito-geral.png" alt="Montagem geral" width="100%"></a>

Montagem com Arduino UNO, chave deslizante (D2), potenciômetro (A0), LED verde (D9), amarelo (D10), vermelho (D11), resistores de 220 Ω e buzzer (D8).

## 02 — Inicialização do sistema

<a href="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/02-inicializacao.png"><img src="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/02-inicializacao.png" alt="Inicialização do Arduino no Tinkercad" width="100%"></a>

Com a simulação iniciada e a chave na posição de energia disponível, o Monitor Serial registra `TV-001;INICIO;monitorando a energia`. O LED verde indica energia normal.

## 03 — Queda com prioridade MODERADA

<a href="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/03-prioridade-moderada.png"><img src="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/03-prioridade-moderada.png" alt="Queda com prioridade MODERADA" width="100%"></a>

Com bateria simulada em 100%, a queda gera `TV-001;QUEDA;bateria=100;restante=480;prioridade=MODERADA`. A autonomia acima de 360 minutos corresponde à faixa MODERADA; o LED amarelo sinaliza a queda.

## 04 — Queda com prioridade ALTA

<a href="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/04-prioridade-alta.png"><img src="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/04-prioridade-alta.png" alt="Queda com prioridade ALTA" width="100%"></a>

Depois do restabelecimento e de uma nova queda com bateria em 75%, o Monitor Serial registra `TV-001;QUEDA;bateria=75;restante=360;prioridade=ALTA`, seguido por mensagens com `restante=345`, `330` e `315`, mantendo `bateria=75`. O LED amarelo sinaliza essa prioridade, válida de 121 a 360 minutos restantes.

## 05 — Queda com prioridade CRITICA e buzzer

<a href="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/05-prioridade-critica-buzzer.png"><img src="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/05-prioridade-critica-buzzer.png" alt="Queda com prioridade CRITICA e buzzer" width="100%"></a>

Após nova queda com bateria simulada em 13%, o Monitor Serial registra `TV-001;QUEDA;bateria=13;restante=62;prioridade=CRITICA` e depois `bateria=13;restante=47;prioridade=CRITICA`. A captura mostra a indicação de emissão de som no buzzer. A faixa CRITICA abrange autonomia de até 120 minutos, com LED vermelho e alerta sonoro.

## 06 — Histórico do Monitor Serial

<a href="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/06-monitor-serial-consolidado.png"><img src="./Tomada_Vital_2_0_Prints_Atualizados_GitHub/06-monitor-serial-consolidado.png" alt="Histórico do Monitor Serial" width="100%"></a>

O histórico mostra as quedas MODERADA (100%), ALTA (75%) e CRITICA (13%), com `TV-001;RESTABELECIDO` entre elas. Em cada queda, o percentual de bateria se mantém constante enquanto a autonomia diminui.

## Critérios de validação

| Cenário | Comportamento esperado |
|---|---|
| Energia disponível | LED verde |
| Queda; mais de 360 min restantes | MODERADA, LED amarelo |
| Queda; entre 121 e 360 min | ALTA, LED amarelo |
| Queda; até 120 min | CRITICA, LED vermelho e buzzer |
| Energia restabelecida | LED verde, buzzer desligado e mensagem `RESTABELECIDO` |

A simulação usa a equivalência de **1 segundo = 1 minuto** de autonomia.

**Limitações:** os sensores de energia e bateria são representados por uma chave e um potenciômetro. Não há ligação celular/MQTT implementada no Tinkercad. A autonomia e as prioridades são parâmetros didáticos, não critérios clínicos validados.

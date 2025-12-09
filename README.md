# Robô Móvel Terrestre 4WD - Sistema de Controle Hierárquico

[cite_start]Este repositório contém o firmware e a documentação para o projeto de controle de um robô móvel terrestre com tração nas 4 rodas (4WD), desenvolvido para a disciplina de Laboratório de Sistema de Controle (Semestre 2025/2)[cite: 41, 42].

[cite_start]O projeto implementa uma arquitetura de **controle hierárquico**, variando desde o controle PID de baixo nível para a velocidade das rodas até o controle cinemático de posição (P2P)[cite: 47].

## 📋 Objetivos do Projeto

O objetivo principal é o desenvolvimento e embarque de um sistema de controle capaz de garantir:
1.  [cite_start]**Controle de Velocidade (Low-level):** Erro nulo ao degrau e tempo de acomodação < 100ms para cada motor[cite: 48].
2.  [cite_start]**Controle de Posição (High-level):** Navegação ponto-a-ponto com erro nulo[cite: 49].
3.  [cite_start]**Controle Longitudinal e Lateral:** Estabilização e seguimento de referência de velocidade[cite: 50, 51].

## ⚙️ Arquitetura de Controle

O sistema opera em malha fechada utilizando uma estratégia em cascata (Hierárquica):

### 1. Nível Superior: Controlador de Posição (P2P)
Responsável por gerar as referências de velocidade linear ($v$) e angular ($\omega$) para que o robô alcance a coordenada alvo $(x_{ref}, y_{ref})$.
* **Entrada:** Pose atual $(x, y, \theta)$ e Alvo $(x_{ref}, y_{ref})$.
* **Saída:** Velocidade linear $v$ e angular $\omega$.
* **Lógica:** Erro de distância define $v$; erro angular define $\omega$ (Lei de controle proporcional).

### 2. Nível Intermediário: Cinemática Inversa
Converte as velocidades globais do robô ($v, \omega$) em velocidades angulares individuais para cada roda ($\omega_{ref}$), considerando a geometria do robô.
* **Modelo:** Tração Diferencial (Skid-steering para 4 rodas).
* **Equações:** Baseadas na relação entre velocidade linear, angular e dimensões do chassi ($R$ e $L$).

### 3. Nível Inferior: Controladores PID (Motor)
Quatro controladores PI independentes que garantem que cada roda gire na velocidade solicitada pela cinemática.
* **Feedback:** Leitura de Encoders (Pulsos $\rightarrow$ rad/s).
* **Atuador:** Ponte H (Sinal PWM).
* **Ganhos Atuais:** $K_p = 4.0$, $K_i = 15.0$.

## 🛠️ Hardware e Pinagem

O projeto utiliza um microcontrolador (Arduino) interfaceado com drivers de potência e sensores.

| Componente | Função | Pinos (Arduino) |
| :--- | :--- | :--- |
| **Motor 1 (FE)** | Tração Frente-Esquerda | IN: 2, 3 \| PWM: 10 |
| **Motor 2 (FD)** | Tração Frente-Direita | IN: 6, 7 \| PWM: 11 |
| **Motor 3 (TE)** | Tração Trás-Esquerda | IN: 4, 5 \| PWM: 12 |
| **Motor 4 (TD)** | Tração Trás-Direita | IN: 8, 9 \| PWM: 13 |
| **Encoders** | Feedback de Velocidade | (Virtual na Simulação) |

*Nota: O código atual inclui uma rotina de `simularPulsos()` para validação da lógica de controle sem o hardware físico ("Hardware-in-the-Loop" simulado).*

## 🚀 Como Executar

1.  **Pré-requisitos:** Arduino IDE instalada.
2.  **Configuração:**
    * Verifique as constantes físicas no início do código (`R`, `L`, `CPR`) para garantir que coincidem com o robô real.
    * Se for usar no robô real, remova a chamada da função `simularPulsos()` e `simular_atrito` e configure as interrupções reais dos encoders.
3.  **Monitoramento:**
    * Abra o **Serial Plotter** ou **Serial Monitor** (Baudrate: 115200) para visualizar a odometria em tempo real: `x`, `y`, `theta`.

## 📊 Resultados Esperados

O robô deve ser capaz de receber uma coordenada $(X, Y)$, girar em direção ao alvo e deslocar-se até ele, corrigindo perturbações de trajetória autonomamente.

## 📚 Referências

* [1] Roteiro: "Segundo Projeto de Laboratório de Sistema de Controle - Semestre 2025/2".
* [cite_start][2] M. Crenganis et al., "Mechatronic Design of a Four-Wheel drive mobile robot and differential steering", MATEC Web of Conferences, 2021[cite: 84].

---
*Desenvolvido pela equipe [Nome da Equipe] para a disciplina de Sistemas de Controle.*

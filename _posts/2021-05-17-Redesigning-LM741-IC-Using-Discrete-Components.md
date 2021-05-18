---
layout: post
title: Redesigning the LM741-OP AMP using discrete components

image: 

---

# Motivation

I have a bunch of BJTs and MOSFET transistors lying around in my special drawer. I thought it would be cool if I could assemble these components to remake the all-popular LM741 integrated operation amplifier in a breadboard. The catch will 
be that I explain all the circuit theory that is used to describe the functionality of each and every block. This is an excellent exercise, in my view, that would 
force me to master the theoretical knowledge, and the practical design considerations. It also emphasizes simulation in LtSpice and sometimes in MATLAB. 

## Analysis

The LM741 has quite complicated circuitry, the analysis of which will be extremely complicated if working with first principles. To simplify the analysis, it is useful to think of
the overall opamp as consisting of an interconnection of several stage each of with defined standalone characteristics and relation with linked circuitry. Functionally, we need biasing circuitry throughtout the op amp to enable correct small signal operations. We need voltage gain stage that will amplify the small signal input voltage by some good amount. We need an output stage to provide enough output power for the op amp to drive loads. 

## Biasing

Working with BJTs, we have three regions of operation. The cutoff region is useless for voltage amplifying applications as the transistors is simply off, the saturation region is also useless since any incremental change in the $$  V_{be} $$ results to no change in the $$ I_{d}$$, on the other hand, in the active region,  despite the exponential dependence of the collector current on the base-emitter voltage, in the limit, applying a small incremental voltage on top of the active-bias voltage, leads to a proportial incremental increase in the collector current on top of the active-bias collector current and thus enables us to define the gain of the amplifier in terms of how much this proportional increase is. Biasing several transistors by applying a $$V_{be}$$ would require a many voltage sources attached to all these transistors, or a a common voltage with several voltage dividing resistors, all too inefficient. The use of current mirror circuits shines in this scenenario as they enable us to mimic some reference collector current to some other transistors with a factor dependent on the relationship between their process technology parameters (the emitter-base junction areas of the two transistors in this case). 

We first create a current source using diode connected pnp and npn transistors with their collectors connected via a resistor $$R_s$$. To determine the amount of the reference current $$I_{REF}$$, we first consider what the base emitter voltages will be for the two transistors Q1 and Q2. Since the collector currents of both Q1 and Q2 are exponentially dependent on $$V_{BE}$$ and the same current has to flow through both resistors, this necessarly implies that $$ V_{BE1} = V_{BE2}$$ . Furthermore, since $$V_{BE}$$ is a logarithmic function of the collector current, with reasonable values for $$I_{REF}$$ (a few milliamps) , the  value of $$V_{BE}$$ can be shown to be approximately 0.7V. This assumption is useful in the design of current source because now we can write a voltage loop equation to solve for $$I_{REF} $$ i.e  $$ V_{CC} - V_{BE} - I_{REF} R_s -V_{BE}+V_{CC}=0 $$ which simplifies to  $$ \begin{equation} I_{REF}= 2(V_{CC}- V_{BE})/R_s \end {equation} $$. Note that the collector base voltage drop $$ V_{CB} $$ is zero in both transistors as they are diode-connected(base and collector are short-circuited). The positive and negative power supply voltage is assumed to equal in magnitude and our assumption $$  V_{BE}=0.7V $$ is justified by a proper choice of $$ R_s $$ that would give the current $$I_{REF}$$ in the milliamps range. Alternatively, we could figure out the exact value of $$I_{REF}$$ and $$  V_{BE}$$ using the load-line method for which the operating point is the intersection of the resistor IV curve and the two transistor's IV curve. The downside of this method is that we need to know the resistor value before-hand or otherwise determine by iteration. 


The convinince of using Q1 transistor in the design of the current source is that we are able to source an extra current steering circuit on top of the diode-connected Q2 transistor. Since a BJT transistors can be modelled using current sources, resistors and diodes, it is understood that the short-circuiting the collector and base results reduces the transistor model to a diode model which is a voltage dependent current source controlled by $$ V_{BE} $$ . Additionally, the base-collector short circuit ensures that the transistors is always in the active region as $$ V_{BE(0.3V)}> V_{CE_sat (0.3V)} $$ . Once $$ V_{BE} $$ is established in transistor Q1, the parallel connection to transistor Q3 would ensure that $$ V_{BE3}=V_{BE2} $$ if resistor $$ R_e=0 $$ and in turn $$ I_{REF}== I_{D3} $$ would hold if the transistors are identical (thus the current mirroring action). The addition of the resistor at the emitter of the Q3 gives the current mirror a special name i.e. "Widlar current source". This current mirror introduces an advantage over the conventional current mirror circuit which is a reduction in the magnitude of resistor $$ R_s $$ . 


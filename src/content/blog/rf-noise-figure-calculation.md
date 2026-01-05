---
title: "Calculation of Noise Figure of a Radio receiver"
description: "Experimental method to calculate noise figure using USRP device"
pubDate: 2019-04-12
tags: ["RF"]
draft: false
---

# Noise Figure Calculation

I got a USRP device at hand and the post uses it as the radio whose noise figure has to be estimated. The method described below is generic and is valid for any RF receiver.

Noise Figure = 10*log₁₀(Noise Factor)

Noise Factor = SNR_IN / SNR_OUT

Following approach is taken to calculate the NF experimentally:

* Provide input to the USRP RIO using a known signal generator with a low amplitude ~-70dBm. This signal should be measured by a VSA or SA to check the signal input to USRP RIO.
* Set the gain setting on USRP Receive channel to maximum, G = 95 dB
* Set the BW of capture to be 10 MHz
* Set the number of points to be captured to be 1024
* Acquire signal from a USRP channel and plot the power spectrum to observe the signal level
* Ensure that the USRP is not running in saturation with the given gain and the input signal. This can be verified by changing the input signal by 10dB and observing an approx 10dB change in the spectrum captured. Do ensure that the signal is above the noise floor as seen in the power spectrum
* Remove any input signal from USRP input and put a 50 Ω termination at the input. Now acquire the spectrum using the same settings as before to observe the noise floor.
* Apply the following formula for calculation

Noise Figure = 10*log₁₀(N_out / (N_in * Gain)) ... (1)

N_out and N_in are both calculated in per Hertz.

10log₁₀(N_in) = 174dBm/Hz ... (2)

10*log₁₀(N_out per Hertz) = Noise Floor(dBm) - 10*log₁₀(Effective Noise Bandwidth)

Effective Noise Bandwidth = (Sampling Bandwidth / FFT points) * Window Scaling Factor

[FFT Scaling For Noise](https://www.ap.com/technical-library/fft-scaling-for-noise/) contains the details of the Scaling Factor that should be used for different window types. Default window used in "FFT Power Spectrum and PSD VI" used in "Rx Streaming(Four Channel)" is hanning, for with Scaling factor of 1.5. Thus

Effective Noise Bandwidth = (10 MHz / 1024) * 1.5 = 14648.43 Hz

10*log₁₀(Effective Noise Bandwidth) ≈ 41.65 ... (3)

10*log(Gain) = Power Observed in Spectrum - Input Power Level ... (4)

Say this as K. Find average noise floor from the power spectrum, say this as N

Plugging (2),(3),(4) in 1 we get

NF = N - 41.65 + 174 - K

This is an approximate calculation as the noise floor is generally not constant throughout, a bunch of RMS averaging should be done to make the noise power measurement. This will give a more accurate N used above and hence a more accurate NF.


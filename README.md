# SFND_Radar_Target_Detection

# ** Radar Target Generation and Detection **

Writeup
---

** Implementation steps for the 2D CFAR process **

As described in the project videos, we want to suppress the noise using 2D CFAR technique. The CFAR is run over the Range Doppler map considering different CUT in each iteration - one 'for' loop runs over the range dimension and the other runs over the doppler dimension. The inner 2 'for' loops are used to sum up the noise over just the training cells.
The average of the training cells noise is taken by dividing by the number of training cells: (2*Tr+2*Gr+1)*(2*Td+2*Gd+1) - (2*Gr+1)*(2*Gd+1).
This number is converted back to logarithmic (dB) units and added with the offset to use as the threshold. If the CUT signal is less than the threshold, it is filtered out, else it is set to 1.

** Selection of training, guard cells and offset **

Tr = 8, Td = 4, Gr = 4, Gr = 4, offset = 10
For lower values of Tr and Td, the offset added had to be increased in order to make sure that the false alarms were filtered. Since in this case, we don't have dense/closely spaced targets, there is no need to use low values of Tr and Td and limit the training cell grid.

Tr = 12, Td = 8, Gr = 4, Gr = 4, offset = 8 
With slightly higher values of Tr and Td, the noise from neighboring cells averaged and so a smaller offset gave a good filtering of false alarms and showed the fft signal peak in the right range.

Tr = 18, Td = 12, Gr = 4, Gr = 4, offset = 8
Tr = 24, Td = 20, Gr = 4, Gd = 4, offset = 8

Increasing the Tr and Td values beyond this point to factor in more training cells did not really help much. I suspect that this is because the noise component in the original fft output signal is fairly low, so trying to average it out over a larger area is not needed.

Since the leakage of the target signal into surrounding cells seems low based on the spike in the fft output RDM, the guard band with Gr = 4 and Gd = 4 as uses in the project video gave good results.
The final values used in this project were: Tr = 12, Td = 8, Gr = 4, Gr = 4, offset = 8

** Steps taken to suppress non-thresholded cells at edges **
To suppress the non-thresholded cells to zero, I created a separate vector RDAM_cfar to store the result after the thresholding. RDAM_cfar is initialized to zero and only the signal points above the threshold are set to 1 in RDAM_cfar.

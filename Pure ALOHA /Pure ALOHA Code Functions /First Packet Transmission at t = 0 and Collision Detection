function [collision, totalTransmissions, successfulTransmissions, transmittingStations] = transmitting_first_packet_at_t_zero_n_col_det(q,packetArrivalLog,frameTransmissionTime, successfulTransmissions, transmittingStations, randomTransmittingStations,totalTransmissions)

% Define the current time at the q-th element of the packetArrivalLog vector
currentTime = packetArrivalLog(q,1);
% Logs the transmittingStations vector with a randomly
% selected station
transmittingStations = [transmittingStations ; randomTransmittingStations(q)]; 
fprintf("Station %d transmits a new packet at t = %.f \n",transmittingStations(q),currentTime)

% Logs the transmition in the totalTransmissions vector
totalTransmissions = totalTransmissions +1;

% if the time window between the currently transmitted packet and the 
% subsequent packet to be transmitted is greater than or equal to the frame
% transmission time, then the transmission is declared successful
% declared successful, otherwise, retransmission is required
            
if (packetArrivalLog(q+1) >= currentTime + frameTransmissionTime)

    successfulTransmissions = successfulTransmissions +1;
    fprintf("Station %d transmitted successfully \n",transmittingStations(q))
    collision = 0;
                    
else
    collision = 1;
end 

end

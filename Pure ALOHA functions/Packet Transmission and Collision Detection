function [collision, totalTransmissions, currentTime, transmittingStations, successfulTransmissions] = Packet_Transmission_n_Collision_Detection(q, totalTransmissions, packetArrivalLog, transmittingStations, randomTransmittingStations, retransmissionQueue, successfulTransmissions, frameTransmissionTime)

% Logs the transmition in the totalTransmissions vector
totalTransmissions = totalTransmissions +1;
currentTime = packetArrivalLog(q,1);
% Checks the catogory of the transmitted packet, 
% 0 --> original, not-zero --> re-transmitted  

if (packetArrivalLog(q,2) == 0)


    % Logs the transmittingStations vector with a randomly selected station
    transmittingStations = [transmittingStations ; randomTransmittingStations(q)]; 
    fprintf("Station %d transmits a new packet at t = %.7f \n",transmittingStations(q),currentTime)

else 

    % determines the index of the retransmissionQueue
    % structure corresponding to the retransmitting station
    num = packetArrivalLog(q,2); 
  
    transmittingStations = [transmittingStations; retransmissionQueue(num).Station];
    fprintf("Station %d is re-transmiting at t =%.7f \n",retransmissionQueue(num).Station,retransmissionQueue(num).new_trans_t)
end

% if the time window between the currently transmitted packet and the 
% previously transmitted packet is greater than or equal to the frame  
% (and) the subsequent packet to be transmitted is greater than or equal to the frame
% transmission time, then the transmission is declared successful
% otherwise retransmission is required

if (packetArrivalLog(q+1) >= frameTransmissionTime + currentTime) && (packetArrivalLog(q-1) + frameTransmissionTime <= currentTime) 

    successfulTransmissions = successfulTransmissions +1;
    fprintf("Station %d transmitted successfully \n",transmittingStations(end))
    collision = 0;

         
else
    collision = 1;
end
end

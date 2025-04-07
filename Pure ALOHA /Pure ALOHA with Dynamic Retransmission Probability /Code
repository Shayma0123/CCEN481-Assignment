clc;clear
frameTransmissionTime = 1e-3; % 1ms
AveragePacketArrivalRate = [100 200 350 500 600 750 850 1000 1500 2000 3000]; %  in Packets per second 
numUsers = 1200;
OfferedLoad = AveragePacketArrivalRate*frameTransmissionTime; % Normalized Offered Load 

for m = 1:length(AveragePacketArrivalRate)

    maxRetransmissions = [ 0 1 3 7]; % Sets the maximum number of re-transmissions attempts

    for a = 1:length(maxRetransmissions)
        
        % The total number of transmissionsv(retransmissions + original) for
        % the total simulation time for each (lambda(m), kmax(a)) pairs
        totalTransmissions = 0;
        totalRetransmissions = 0; 
        successfulTransmissions = 0;
        maxPropagationDelay = 3.33e-6; 
        % Defines a structure for storing the info. of the retransmitting
        % stations
        retransmissionQueue = struct('Station', {}, 'k_attempts', {}, 'new_trans_t', {});
        % Defines an array to log the transmitting stations  
        transmittingStations = [];
        % Sets the current time to be 0, 
        % Initiolizes the packetArrivalLog vector with (0,0), assuming the
        % first original packet is transmitted at t = 0s 
        packetArrivalLog = [0 0]; currentTime = 0; packetArrivalLog_new =[];
        sim_time = 25; %seconds

        %%%%%%%%%%%%%%%%%%%%%%%%%%%% Module 1: Packet Arrival Module %%%%%%%%%%%%%%%%%%%%%%%
  

        % Creates the packet arrival log vector
        % The exponential distribution generates the inter arrival time between 
        % two given packets
        % Accumalting the generated interarrival time with the previous log
        % produces the time vector with the all the packets logs
       
        while (packetArrivalLog(end, 1) <=sim_time)

            T = exprnd(1/AveragePacketArrivalRate(m),1,1);
            t_bef = packetArrivalLog(end, 1);
            packetArrivalLog = [packetArrivalLog;t_bef+T  0];
        
         end
         randomTransmittingStations = randi([1 numUsers], 3*length(packetArrivalLog),1);


          %%%%%%%%%%%%%%%%%%%%%%%%%%%% Module 2: Collision Detection %%%%%%%%%%%%%%%%%%%%%%%

         Ft_s = 1;  
         while (Ft_s <= sim_time/frameTransmissionTime) % number of frame transmission time slotts


             indices = find((packetArrivalLog(:,1) >= frameTransmissionTime * (Ft_s - 1)) & ( packetArrivalLog(:,1) < frameTransmissionTime * Ft_s));

             % tracks the number of stations transmitting every time
             % slot duration equal to the frame transmission time

             for q = 1:length(indices)

                 index = indices(q);
                 if (packetArrivalLog(index,2) == 0)

                     % specifies the catogory, 0 for original packets 
                     transmittingStations = [transmittingStations; randomTransmittingStations(index) 0]; 
                     fprintf("Station %d is transmitting an original packet during the %d-th slot \n", transmittingStations(end,1),Ft_s);
                 else
                   
                      num = packetArrivalLog(index ,2);
                      transmittingStations = [transmittingStations; retransmissionQueue(num).Station num];
                      fprintf("Station %d is re-transmitting a packet during the %d-th slot \n", transmittingStations(end,1), Ft_s);
                 end    
             end

             % increments the slot count
             Ft_s = Ft_s+1;
                
             % counts the number of transmitting stations during a given slot
             [rows, c ] = size(transmittingStations);
             % tracks the number of total transmissions 
             totalTransmissions = totalTransmissions + rows ;

             if (rows == 1)  % if only one station transmits during the slot

                 fprintf("Station %d transmits successfully \n",transmittingStations(1,1))
                 successfulTransmissions = successfulTransmissions +1;
                          
             else
                 if (maxRetransmissions(a) ~= 0)

                     for n = 1: rows % for the number of the stations transmitting during the same time slot


                         if (transmittingStations(n ,2) == 0)

                             %%%%%%%%%%%%%%%%%% Module 3: Collision Handling for Original Packets %%%%%%%%%%%%%%%%%%
  
                             fprintf("Station's %d packet collides with anther packet. The station waits for re-transmission \n",transmittingStations(n,1))
                             % creates a new element of the retransmissionQueue structure 
                             newRetransmission.k_attempts = 1;
                             newRetransmission.Station = transmittingStations(n,1);

                             % Uses BEB method to calculate the next transmission time 
    
                             R = randi([0 2^(newRetransmission.k_attempts-1)]);
                             currentTime = (Ft_s-1)*frameTransmissionTime;
                             delay = R*frameTransmissionTime + 2*maxPropagationDelay; 
                             nextTransmissionTime = delay + currentTime;

                             newRetransmission.new_trans_t = nextTransmissionTime;

                             % appends the retransmissionQueue structure with a
                             % new element containing the retransmiting station info.
                             retransmissionQueue(end +1) = newRetransmission;

                             t_waiting_times = [retransmissionQueue.new_trans_t];
                             idx = find(t_waiting_times >= currentTime);
                             n_waiting = length(idx);
                             p_retransmitting = 1/n_waiting;
                             
                             % appends the packetArrivalLog with the station's next transmission time 
                             
                             prop_outcome = (rand(1,1) < p_retransmitting);
                                if (prop_outcome == 1)
                                    
                                    ret_num = numel(retransmissionQueue);

                                    % appends the packetArrivalLog with the station's next transmission time 
                                    packetArrivalLog = [packetArrivalLog; nextTransmissionTime ret_num];
                                    packetArrivalLog = sortrows(packetArrivalLog, 1, 'ascend');
                                     fprintf("Station's %d will re-transmits at t = %.7f \n",transmittingStations(n,1), nextTransmissionTime)
                                end

                             % Saves the index corresponding to the transmitting
                             % staion
                             
                             % appends the packetArrivalLog with the station's next transmission time 
                  

                	         
                         else 

                             %%%%%%%%%%%%%%%%%% Module 3: Collision Handling for Retransmitted Packets %%%%%%%%%%%%%%%%%%

                             num = transmittingStations(n,2);
                             % Increments the number of attempts field of the retransmissionQueue structure
                             retransmissionQueue(num).k_attempts = retransmissionQueue(num).k_attempts +1;
                               
                             if (retransmissionQueue(num).k_attempts <= maxRetransmissions(a))
  
                                 fprintf("Station's %d packet collides with anther packet. The station waits for re-transmission \n",retransmissionQueue(num).Station)
                                    
                                 % Uses BEB method to calculate the next transmission time 
                                 R = randi([0 2^(retransmissionQueue(num).k_attempts)-1]);
                                 currentTime = (Ft_s-1)*frameTransmissionTime;
                                 delay = R*frameTransmissionTime + 2*maxPropagationDelay;
                                 nextTransmissionTime = delay +currentTime;
                                    
                                 % appends the packetArrivalLog with the station's next transmission time
                                 retransmissionQueue(num).new_trans_t = nextTransmissionTime;

                                 t_waiting_times = [retransmissionQueue.new_trans_t];
                             idx = find(t_waiting_times >= currentTime);
                             n_waiting = length(idx);
                             p_retransmitting = 1/n_waiting;
                             
                             % appends the packetArrivalLog with the station's next transmission time 
                             
                             prop_outcome = (rand(1,1) < p_retransmitting);
                                if (prop_outcome == 1)
                                    
                                    
                                    % appends the packetArrivalLog with the station's next transmission time 
                                    packetArrivalLog = [packetArrivalLog; nextTransmissionTime num];
                                    packetArrivalLog = sortrows(packetArrivalLog, 1, 'ascend');
                                    fprintf("Station's %d will re-transmits at t = %.7f \n",retransmissionQueue(num).Station,retransmissionQueue(num).new_trans_t)

                                end
                                 
                                 
                                   
                             else 
                                 fprintf("Maximum number of attempts is reached for station %d \n", retransmissionQueue(num).Station)
                                    
                             end
                         end
                     end
                 end           
            end
        transmittingStations = [];
        end
    Throughput(a,m) = (successfulTransmissions/Ft_s);
    end
end

plot(OfferedLoad, 100*OfferedLoad.*exp(-OfferedLoad), OfferedLoad, 100*Throughput)
legend('Theoretical Slotted ALOHA','Max Retransmissions = 1','Max Retransmissions = 3', 'Max Retransmissions = 7');
title("Slotted ALOHA: Throughput vs. Offered Load")
ylabel("Throughput (%)")
xlabel("Total Offered Load")
grid on
